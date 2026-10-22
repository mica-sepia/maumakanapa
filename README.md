![Logo](https://maumakanapa.art/static/imgs/maumakanapaweb.png)
# `maumakanapa.art` // your place to read labirhin's comics!

## What is this website?
maumakanapa.art is a website where people can read Labirhin's Mau Makan Apa comic!\
It also has some links that redirect to his Discord, merch website, Patreon, etc
Currently it has the following languages (both for the website & the webcomic):
* Indonesian
* English
* Spanish
* Russian
* Italian
* Brazilian Portuguese
* Chinese
* Filipino
* Ukrainian

## What is this repository?
This repository is literally just the website it self, I decided to make it open-source because, well, I love making everything open-source.

## What does the website currently have?
Not only does the website have stuff about, well, Mau Makan Apa, it also has info about:
* Revision after Revision
* The Stars Are Falling
* The Big Tempeh Lie
* Missile City

## Episode comments and staff posts
Episode pages include anonymous comments, reports, and moderation. Visitors can report a post with a reason; reporting flags it for staff review without automatically hiding it. Automatic screening holds likely profanity, direct threats, or link spam.

Anonymous ownership uses a random, HttpOnly, SameSite=Lax browser cookie that lasts one year, rather than only a short-lived Django session. It lets the same browser retain edit/delete access after the session expires. Clearing browser cookies still loses anonymous ownership. The nullable `EpisodeComment.author` relation and `claim_browser_comments_for_authenticated_user(request)` service in `comics_app.services` prepare for a later login flow: after a user authenticates, the future login integration can call this server-side helper to claim comments tied to the browser's ownership cookie. No public login or account-claim endpoint is enabled yet.

### Moderator setup and review
From the project directory, run migrations and provision the least-privilege group:

```powershell
python manage.py migrate
python manage.py setup_comment_moderators
```

In Django admin, assign moderators to **Comment moderators** and enable their **Staff status**. Do not make them superusers. The group can view comments and reports, approve/hide comments, dismiss reports, delete rule-breaking comments, and view the audit log; it cannot create comments or edit their text. Staff must sign in with their Django staff account to post. Their verified posts show the orange gradient `[STAFF]` badge, and staff can choose **Post as Anonymous** while retaining the badge and account ownership.

Open **Episode comments → Moderation queue** to see reported and automatically held comments together. Open a comment to change its **Approved** checkbox; approval is a per-comment decision, not a bulk action. On **Comment reports**, use **Dismiss selected reports (keep comments)** for reports that do not indicate a rule violation. For rule-breaking posts, use **Delete reported comment(s) and replies**; this removes the comment, replies, and associated reports. Moderation actions are retained in the read-only **Comment moderation events** admin page with the actor, action, timestamp, and comment ID; comment contents and report reasons are deliberately not copied into the audit log.

### Upload, retention, and production operations
Comment attachments accept JPEG, PNG, GIF, and WebP images up to 5 MB, 20 megapixels, 10,000 pixels per side, and 100 animation frames. Videos and other formats are rejected. These limits are configurable in `maumakanapa/settings.py`. Production must serve `MEDIA_ROOT` through a proper media host/object store; Django's development media route is only enabled when `DEBUG` is on.

Reports are retained for 365 days by default. Schedule this command (for example, monthly) to delete expired report records and clear the comment's reported flag when no reports remain:

```powershell
python manage.py prune_comment_reports
```

Comment attachments remain until their comment is deleted. Back up the SQLite database and all of `MEDIA_ROOT` together. For local SQLite deployments, the following command creates a ZIP containing a consistent database snapshot and the media files; keep the archive outside the media directory:

```powershell
python manage.py backup_site_data "C:\Backups\maumakanapa-2026-10-22.zip"
```

Store backups encrypted with restricted access, define a separate backup expiry policy, and periodically test restoring both the database and media files. For a release or restore, stop comment writes while taking/restoring database and media backups so the two stay aligned. The backup command currently supports SQLite only.

The current SQLite database and local-memory cache are suitable for local and single-process testing. Before public hosting, set a unique `DJANGO_SECRET_KEY`, `DJANGO_DEBUG=0`, configure HTTPS and a production media host, then run `python manage.py check --deploy`. If you later run multiple app workers, SQLite write locking and per-process local-memory rate limits become constraints; plan a shared cache for rate limits and evaluate a server database then. No Kubernetes setup is required.

## Is there any monitization?
No, I, orangecatindustries, do not get monitized for anything in this website, I made the website just for fun and archival, I do not have plans for monitization.

## What do we need to host this?
You will need the following:
* Pillow
* django-admin-sortable2
* django-minify-html
* django-compressor
* gunicorn (if you want to host via linux)
* whitenoise (if you want to host via windows)

## Do you have permission to host the webcomic?
I have talked with higher ups about my website, and it is now considered an official website to read the Mau Makan Apa comic, so technically, yes, I do have permission.

## License & Copyright

### Website Source Code
The custom code, layout, and software frameworks created for this website are licensed under the **GNU General Public License v3.0 (GPLv3)**. 

Under this license, you are free to copy, modify, and distribute this website's code, provided that:
* You credit me, orangecatindustries.
* Any modified versions or derivative websites you build **must also be open-sourced under the exact same GPLv3 license**.

### Disclaimer!
**The GPLv3 license DOES NOT apply to the webcomic hosted on this website.** 

All comic pages, artwork, characters, story elements, and related media are intellectual property of **Labirhin**. 
* You do **NOT** have permission to copy, scrape, redistribute, or monetize the comic assets. 
