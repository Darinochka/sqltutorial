# Basic Requirements

**1. Which tracks appeared in the most playlists? how many playlist did they appear in?**

```
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

```
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
3) 
