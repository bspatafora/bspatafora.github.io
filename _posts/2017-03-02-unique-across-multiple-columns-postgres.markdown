---
layout: post
title:  "Ensuring an (integer) value is unique across multiple columns in Postgres"
date:   2017-03-02
---

The secret is a relatively little-discussed Postgres feature called [exclusion constraints](https://www.postgresql.org/docs/current/static/ddl-constraints.html#DDL-CONSTRAINTS-EXCLUSION).

Exclusion constraints are like unique constraints on steroids. You can “reimplement” a unique constraint using an exclusion constraint:

```
CREATE TABLE users (
    email string,
    EXCLUDE USING gist (email WITH =)
);
```

`EXCLUDE` starts the statement. `USING gist` indicates you want to use a GiST index, which seems necessary to use exclusion constraints. To use this index, you’ll need to have installed the btree_gist extension:

```
CREATE EXTENSION IF NOT EXISTS btree_gist
```

`(email WITH =)` defines the actual constraint. In this case, we’re excluding duplicate entries on the email column based on equality, just like a uniqueness constraint.

But exclusion constraints are more general than uniqueness constraints and can therefore do more. Onto the constraint described in the title. To construct this sort of “uniqueness across multiple columns” constraint, we need to install the intarray extension:

```
execute "CREATE EXTENSION IF NOT EXISTS intarray"
```

Here’s the form the constraint took for me. I needed a constraint like this because in this system, a user should only be a member of a single conversation, and a given user can appear arbitrarily as either the `left_user` or the `right_user` in their assigned conversation:

```
CREATE TABLE conversations (
    left_user_id integer,
    right_user_id integer,
    EXCLUDE USING gist ((array[left_user_id, right_user_id]) WITH &&)
);
```

Here, we’re building an `intarray` comprised of the two `user_id` fields, and using that array as the basis for comparing new entries to existing ones. The operator we’re using, `&&`, will return `true` if there is any overlap between the `user_id` array of the new entry and the `user_id` array of any existing entry.

In my system, this constraint is only true for a given `start_time`. So the actual constraint I used was this:

```
CREATE TABLE conversations (
    left_user_id integer,
    right_user_id integer,
    start_time timestamp,
    EXCLUDE USING gist (start_time WITH =, (array[left_user_id, right_user_id]) WITH &&)
);
```

I include this to show that you can have more than one constraint. If all constraints evaluate to `true`, the new entry will be rejected.

Unfortunately, it sounds like this is only possible with integers at the moment, as there isn’t a GiST operator family for general arrays.
