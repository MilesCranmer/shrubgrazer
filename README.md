# shrubgrazer

Alternative Mastodon UI.  Very much in progress.

Intended as a prototype and demonstration, not something I expect lots
of people will use.

Current status:
* Displays feed: https://www.jefftk.com/shrubgrazer/
* Displays posts: https://www.jefftk.com/shrubgrazer/post/[id]
  * Tree-style display of posts
* Displays history: https://www.jefftk.com/shrubgrazer/history
* Tracks which posts you've seen
* "Infinite" scroll
  * Not really: at some point it tells you you're done
  * But it does load more entries dynamically as you scroll down
* Prioritized feed

Algorithmic feed implementation:
* Context: https://www.jefftk.com/p/user-controlled-algorithmic-feeds
* Track which entries have been on screen for at least a second
  * "Unviewed" entries are ones that haven't been
* Per-person priority scores
  * Can click on posts to up/down priority, plus there's a control panel
* Feed is a prioritized list of unviewed entries:
  * Default prioritization:
    * First sort by user priority
    * Then group by thread
      * This prevents seeing posts in 3/3, 2/3, 1/3 order
    * Then show chronologically
    * For later: give some context for posts
  * Try to make this pluggable, since this is a big place I expect
    preferences to differ.

Next steps:
* Fix bug where you see things multiple times
* Fix bug where somehow I started getting 404 for everything until I restarted
* When viewing a given post's tree, unviewed items should be marked

## Installation

```
nginx.conf:
  location /shrubgrazer {
    include uwsgi_params;
    uwsgi_pass 127.0.0.1:7096;
    add_header Cache-Control "private;max-age=0";
  }

/etc/systemd/system/uwsgi-shrubgrazer.service:
  [Unit]
  Description=uWSGI shrubgrazer

  [Service]
  ExecStart=/usr/bin/uwsgi_python3 --socket :7096 --wsgi-file /home/jefftk/code/shrubgrazer/shrubgrazer.py
  Restart=always
  KillSignal=SIGQUIT
  Type=notify
  NotifyAccess=all

  [Install]
  WantedBy=multi-user.target

$ sudo systemctl enable uwsgi-shrubgrazer
$ sudo systemctl daemon-reload
```
