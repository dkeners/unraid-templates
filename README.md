# dkener's unraid-templates

My Unraid Community Addon template repository, setup support is found in this README.

- Maybe Finance
  
  - Maybe_Web
    
  - Maybe_Worker
    

# Apps

## Maybe Finance

***Maybe does not officially support this release method***, it is just something I have put together; AS SUCH, if there are issues with the app that are likely due to weird installation config, please raise an issue here or on the [Unraid Forum thread for these apps](https://forums.unraid.net/topic/190003-support-dkeners-maybe-web-worker). We don't need to flood the Maybe issues page with our love of Unraid. However, if you see something that you think is a bug of the software please report it on the Maybe issues page!

#### Install:

**Docker Sub-Net Setup**
  > 💡    ***Tip:*** *I recommend running the app in its own docker sub-net, this limits what the app can happily (or accidentally) talk to. See [#2912](https://github.com/maybe-finance/maybe/issues/2192)*
1. Open the Unraid console, the `>_` in the top right corner.
2. Run the command: `docker network create maybe-stack` Replace maybe-stack with whatever name you want, just be sure to use that for the rest of the guide.

**Redis & PostgreSQL Setup**
  > ✏    ***Note:*** *This app requires you to install both Redis and Postgres in addition to the web and worker apps. Having these misconfigured is maybe the biggest cause of self-hosting problems based off of GitHub issues.
1. Install a **PostgreSQL** app from Community Addons store, any will likely work, I use sgraaf's template.
2. Input the following settings *(names may vary)*:
    - ***Name:*** maybe_db
    - ***Repository:*** postgres:16
    - ***Network Type***: Custom : maybe-stack
    - ***Data Storage Path:*** /mnt/user/appdata/maybe/postgres-data
    - ***DB User:*** maybe_user
    - ***DB Password:*** 1234 *<- Try to do better*
    - ***DB Name:*** maybe_production
3. PostgreSQL will build your database the first time you run the app, changing any database settings after the first build will not change anything. Your database should not be running!
4. Install a **Redis** app from the Community Addons store, again, any will work. I use jj9987's template.
5. Input the following settings *(names may vary)*:
    - ***Name:*** maybe_redis
    - ***Repository:*** redis:latest
    - ***Network Type***: Custom : maybe-stack
    - ***Data Storage Path:*** /mnt/user/appdata/maybe/redis-data
    
**Maybe_Web Setup**
1. Find **Maybe_Web** in the community addon store.
2. Input the following settings *(names may vary)*:
    - ***Name:*** maybe_web *(can be whatever you want to see as the app name)*
      
      > *Maybe is very actively developed, for now it is recommended to stay on latest to get all the new features and fixes. If you want you can pin it to a cetain release by changing `latest` to `0.5.0`*.
      
    - ***Repository:*** ghcr.io/maybe-finance/maybe:latest
    - ***Network Type***: Custom : maybe-stack
    - ***Secret Key:*** *{Your key here!}*
      - To generate the key go to the Unraid console; `>_` in the top right corner.
      - Run `openssl rand -hex 64`.
      - Copy the output and paste in the Seceret Key input.
        
      > *Docker allows us to specify a host by the name of the container, which we defined earlier. If your postgres what not configured using this guide provide the IP (X.X.X.X) or URL (joe.com) of your database.*
      
    - ***Postgres - Host:*** maybe-db
    - ***Postgres - Username:*** maybe-user
    - ***Postgres - Password:*** *{Whatever you used earlier}*
    - ***Postgres - Database:*** maybe-production
    - ***Redis - URL:*** redis://maybe_redis:6379/1
    - ***Config data:*** /mnt/user/appdata/maybe/app-storage
      
      > *To get the following **optional** setting click `Show more settings...`*
      
    - ***OpenAI API Token:*** *{Your OpenAI Token, be happy I dont know it ;)}*
3. Click done, the app should start running, but it won't work yet! you have to configure the Maybe_Worker.

**Maybe_Worker Setup**
1. We don't want to have to enter all that again, find the `Add Container` button in the bottom left of the `Docker` page.
2. In the Add Container form, click the `Select a template` dropdown.
3. In the dropdown, under `User templates` select `maybe_web`.
4. Voilà! It's pre-filled! Change the following settings *(names may vary)*:
    - ***Name:*** maybe_worker
      
      **Turn on ADVANCED VIEW, toggle in top right, will say BASIC VIEW when off**
      
    - ***Post Arguments***: bundle exec sidekiq
    - Click `REMOVE` on the ***Web UI Port:*** input.
      
      **Turn off ADVANCE VIEW**
5. Click done, the app should build and Maybe should be fully functional!
