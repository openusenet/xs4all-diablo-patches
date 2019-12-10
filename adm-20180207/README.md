## Diablo adm scripts in Bourne Shell.

These scripts assume that the diablo installation is in `/news`.

Copy all the scripts in this directory to `/news/adm/` and make them owned by the `news` user.

Then simply add to cron:

```
MAILTO=your_news_admin_email@your-domain.ext
*/5 * * * *   news   /news/adm/cron-5mins
50  * * * *   news   /news/adm/cron-hourly
0   0 * * *   news   /news/adm/cron-daily
```

If you have an `active-file server` that you use to sync the local
active file against, add `actsync=my-active-file-server.mydomain.ext`
as an argument to cron-hourly like so:

```
50  * * * *   news   /news/adm/cron-hourly actsync=my-active-file-server.mydomain.ext
```

