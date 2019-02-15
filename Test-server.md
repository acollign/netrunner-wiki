# Test server

For the ease of testing and development, local development setup as described in the [Getting Started with Development](https://github.com/mtgred/netrunner/wiki/Getting-Started-with-Development) guide is the best. However, sometimes there is a need to test new code in a production-like environment or to get some feedback from other developers before the code is ready for production. The public test server is ideal for this purpose.

## Access

The public test server can be accessed at http://jinteki.zaroth.net . The database is completely separate from the production one, so you need a separate register/login for it.

The server periodically (every 6 hours, at 6,12,18,24 UTC) pulls fresh code from the `dev` branch in this repository and if it detects any new commits, it performs a complete rebuild (recompile and restart). The rebuild and service maintenance can be also performed via SSH (if you have access), for details on available commands check [the sudoers file](https://github.com/zaroth/betajin-pupfiles/blob/master/modules/jinteki/files/sudoers).

**EDIT:* The server cron job for this has been disabled, as it sometimes causes errors. You can run `crontab -e` to list the cron jobs and re-enable it.

For more information on which features/fixes are good material for the `dev` branch and which are better suited for `master`, see [Pull requests and branches](https://github.com/mtgred/netrunner/wiki/Getting-Started-with-Development#pull-requests-and-branches).

## Logs

Logs from the test server are available either via SSH access (mentioned above) or via web interface http://jinteki.zaroth.net:8080/ . They are especially useful for finding stack traces of exceptions in game server process caused by players on the test server (`(DATE)_game_log.txt`).

## Configuration

Most configuration of the test server is stored in the [zaroth/betajin-pupfiles](https://github.com/zaroth/betajin-pupfiles) repository. In case you see a better way of doing something than the one that's listed in these pupfiles, don't hesitate to submit an issue/PR there.