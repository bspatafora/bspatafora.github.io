---
layout: post
title:  "N+1 queries"
date:   2015-01-18
---

I recently worked on a story that involved some calls to a database. This database has sellers, and sellers have products. The story involved displaying all of the products on a page grouped under their respective sellers. I wanted to end up with a hash where each key was a seller and each value an array of that seller’s products.

It seems at first glance like it makes sense to do this:

{% highlight ruby %}
products_by_seller = {}
sellers = Spree::Seller.all

sellers.each do |seller|
  products_by_seller[seller] = seller.products
end
{% endhighlight %}

But consider the performance implications of this. There’s one query to get all the sellers: `Spree::Seller.all` results in the SQL `SELECT spree_sellers.* FROM spree_sellers`. Then there’s an additional query for each seller: `seller.products` results in the repeated execution of a query like the following:

{% highlight sql %}
SELECT spree_products.* FROM spree_products
INNER JOIN spree_sellers_products
ON spree_products.id = spree_sellers_products.product_id
WHERE spree_sellers_products.seller_id = 1
{% endhighlight %}

In other words, this is an n+1 query (one query to get the results to be iterated over, and n queries corresponding to the number of results returned by the first query). This doesn’t create a problem with the small amount of fake data used in development, but in production a query like this could result in the user waiting an unnecessarily long time for all of the sellers’ products queries to execute.

Rather than allowing the number of queries to increase indefinitely as the number of sellers increases, we can use [eager loading][] to keep things at a single query regardless of the number of sellers:

{% highlight ruby %}
products_by_seller = {}
sellers = Spree::Seller.eager_load(:products)

sellers.each do |seller|
  products_by_seller[seller] = seller.products
end
{% endhighlight %}

This results in a query like the following (I’ve simplified it from the original, which specified many more columns than just ID and name for both tables):

{% highlight sql %}
SELECT spree_sellers.id AS t0_r0,
       spree_sellers.name AS t0_r1,
       spree_products.id AS t1_r0,
       spree_products.name AS t1_r1
FROM spree_sellers
LEFT OUTER JOIN spree_sellers_products
ON spree_sellers_products.seller_id = spree_sellers.id
LEFT OUTER JOIN spree_products
ON spree_products.id = spree_sellers_products.product_id
{% endhighlight %}

With large numbers of sellers, spending the time to improve this query pays off in the form of significantly faster page loads for the user and an app that’s that much more resistant to crashing under heavy load.

[eager loading]: http://api.rubyonrails.org/classes/ActiveRecord/QueryMethods.html#method-i-eager_load
