# Basic Requirements

**1. Which tracks appeared in the most playlists? how many playlist did they appear in?**
```sql
select playlist_track.TrackId,
	name,
	count(PlaylistId) as "Number of appearance in playlist"
from playlist_track
join tracks
	on  playlist_track.TrackId = tracks.TrackId
group by 1
order by 3 DESC;
```

**2) Which track generated the most revenue? which album? which genre?**
```sql
select tracks.TrackId,
	tracks.name,
	sum(invoice_items.UnitPrice) as "Revenue",
	genres.name,
	albums.Title
from tracks
join invoice_items
	on invoice_items.TrackId = tracks.TrackId
join genres
	on genres.GenreId = tracks.GenreId
join albums
	on albums.AlbumId = tracks.AlbumId
group by 1
order by 3 DESC;
```

**3) Which countries have the highest sales revenue? What percent of total revenue does each country make up?**

Primarily, we have to find out the total revenue of all countries:
```sql
select sum(Total)
from invoices;
```

Now we can calculate the Total and Percentage each of the country:
```sql
select customers.Country,
	round(sum(invoice_items.UnitPrice),2) as "Revenue",
	round((sum(invoice_items.UnitPrice)/2328.6)*100, 2) as "Persentage"
from invoices
join invoice_items
	on invoice_items.InvoiceId = invoices.InvoiceId
join customers
	on customers.CustomerId = invoices.CustomerId
group by 1
order by 2 DESC;
```
##### Second option

```sql
select customers.Country,
	round(sum(invoices.Total), 2) as "Total",
	round((sum(invoices.Total)/2328.6)*100, 2) as "Percentage"
from customers
join invoices
	on invoices.CustomerId = customers.CustomerId
group by 1
order by 2 desc;
```

**4) How many customers did each employee support, what is the average revenue for each sale, and what is their total sale?**
```sql
select customers.SupportRepId,
	employees.FirstName,
	count(DISTINCT customers.CustomerId) as "Count of support",
	round(sum(invoices.Total)/count(DISTINCT customers.CustomerId), 2) as "Average of revenue",
	round(sum(invoices.Total), 2) as "Total"
from customers
join employees
	on customers.SupportRepId = employees.EmployeeId
join invoices
	on customers.CustomerId = invoices.CustomerId
group by 1;
```
# Intermediate Challenge
**1) Do longer or shorter length albums tend to generate more revenue?**
Find out average of the length of the albums:
```sql
with album_length as(
	select albums.AlbumId,
		sum(tracks.Milliseconds)/60000 as "Length"
	from albums
	join tracks
		on tracks.AlbumId = albums.AlbumId
	group by 1
)
select sum(album_length.Length)/count(album_length.AlbumId)
from album_length;
```
Find out average of the revenue of the albums:
```sql
with revenue_length_album as (
	select albums.AlbumId,
		sum(tracks.UnitPrice) as "Revenue",
	case 
		when (sum(tracks.Milliseconds)/60000) > 65 then "Long"
		when (sum(tracks.Milliseconds)/60000) <= 65 then "Short"
	end as "Length"
	from tracks
	join albums
		on albums.AlbumId = tracks.AlbumId
	group by 1
)
select Length,
	avg(Revenue)
from revenue_length_album
group by 1;
```
```sql
with revenue_length_album as (
	select albums.AlbumId,
		sum(tracks.UnitPrice) as "Revenue",
	case 
		when (sum(tracks.Milliseconds)/60000) > 65 then "Long"
		when (sum(tracks.Milliseconds)/60000) <= 65 then "Short"
	end as "Length"
	from tracks
	join albums
		on albums.AlbumId = tracks.AlbumId
	group by 1
)
select revenue_length_album.Length,
	round(avg(revenue_length_album.Revenue), 2) as "Average of the revenue",
	round((avg(revenue_length_album.Revenue)/28)*100) as "Percentage"
from revenue_length_album
group by 1; 
```
**2)Is the number of times a track appear in any playlist a good indicator of sales?**
```sql
with appereance as (
	select TrackId,
		count(PlaylistId) as "Count"
	from playlist_track
	group by 1
)
select appereance.Count,
	round(sum(invoice_items.UnitPrice)),
	count(appereance.TrackId)
from invoice_items
join appereance
	on invoice_items.TrackId = appereance.TrackId
group by 1;
```
