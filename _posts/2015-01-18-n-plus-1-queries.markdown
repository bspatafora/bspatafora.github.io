---
layout: post
title:  "N+1 queries"
date:   2015-01-18
---

I recently worked on a story that involved some calls to a database. This database has artists, and artists have products (which are mostly albums, but can also be merchandise). The story involved displaying all of the products on a page grouped under their respective artists. I wanted to end up with a hash where each key was an artist and each value an array of that artist’s products.

It seems at first glance like it makes sense to do this:

{% highlight ruby %}
products_by_artist = {}
artists = Spree::Artist.all

artists.each do |artist|
  products_by_artist[artist] = artist.products
end
{% endhighlight %}

But consider the performance implications of this. There’s one query to get all the artists: `Spree::Artist.all` results in the SQL `SELECT spree_artists.* FROM spree_artists`. Then there’s an additional query for each artist: `artist.products` results in the repeated execution of a query like the following:

{% highlight sql %}
SELECT spree_products.* FROM spree_products
INNER JOIN spree_artists_products
ON spree_products.id = spree_artists_products.product_id
WHERE spree_artists_products.artist_id = 1
{% endhighlight %}

In other words, this is an n+1 query (one query to get the results to be iterated over, and n queries corresponding to the number of results returned by the first query). This doesn’t create a problem with the small amount of fake data used in development, but in production a query like this could result in the user waiting an unnecessarily long time for all of the artists’ products queries to execute.

Rather than allowing the number of queries to increase indefinitely as the number of artists increases, we can use [eager loading][] to keep things at a single query regardless of the number of artists:

{% highlight ruby %}
products_by_artist = {}
artists = Spree::Artist.eager_load(:products)

artists.each do |artist|
  products_by_artist[artist] = artist.products
end
{% endhighlight %}

This results in a query like the following (I’ve simplified it from the original, which specified many more columns than just ID and name for both tables):

{% highlight sql %}
SELECT spree_artists.id AS t0_r0,
       spree_artists.name AS t0_r1,
       spree_products.id AS t1_r0,
       spree_products.name AS t1_r1
FROM spree_artists
LEFT OUTER JOIN spree_artists_products
ON spree_artists_products.artist_id = spree_artists.id
LEFT OUTER JOIN spree_products
ON spree_products.id = spree_artists_products.product_id
{% endhighlight %}

With large numbers of artists, spending the time to improve this query pays off in the form of significantly faster page loads for the user and an app that’s that much more resistant to crashing under heavy load.

[eager loading]: http://api.rubyonrails.org/classes/ActiveRecord/QueryMethods.html#method-i-eager_load
