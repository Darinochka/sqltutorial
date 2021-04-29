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

Primarily, we have to find out the total revenue of all countries:
```sql
select sum(Total)
from invoices;
```

Now we can calculate the Total and Percentage each of the country:
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
