Bingosync.com
===

This is the repository powering [bingosync.com](http://bingosync.com/),
a web application that lets people collaboratively work on "bingo boards"
when speedrunning games.

For more information on speedrunning and bingo, you can read:
  - [the bingosync about page](http://bingosync.com/about)
  - [the speedrunslive faq](http://www.speedrunslive.com/faq/)
  - [the about section of an SRL bingo card](http://www.speedrunslive.com/tools/oot-bingo/)

#### Fun Implementation Details! :D

Bingosync is implemented using a combination of the [django](https://www.djangoproject.com/)
and [tornado](http://www.tornadoweb.org/) web servers. The django web server
(bingosync-app) hosts the main website content, serves most of the pages,
and talks to the database. The tornado web server (bingosync-websocket)
maintains all websocket connections to the site and passes messages along
to the clients in a "publish and subscribe" kind of model.

The actual site is hosted on one of my personal machines. It's running behind 
an [nginx](http://wiki.nginx.org/Main) proxy that serves static files and splits
traffic to the django and tornado servers. I use [postgres](http://www.postgresql.org/)
for the database. Conveniently, this machine is the same one that I run 
[bingobot](https://github.com/kbuzsaki/bingobot) off of. Maybe there's some 
opportunity for integration there in the future :)

### Running on local machine
There are a couple of pre-requiesets to run this on your local machine:
* Install nodejs if you don't have it (`brew install node` or `sudo apt install nodejs`)
* Setup a virtual env for the app. Run `python -m venv venv`
* Source the virtual environment. Run `source venv/bin/activate`
* Edit the `requirements.txt` file, and remove the `psycopg2` dependency, then run `pip install -r requirements.txt`
* Edit the file `bingosync-app/bingosync/settings.py`
 * Remove the import of `DB_USER` from `secret_settings.py`
 * Change the `DATABASES` settings to the following:
```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join('database','db.sqlite3')
    }
}
```
* Create a file `bingosync-app/bingosync/secret_settings.py` and enter the following into it:
```
SECRET_KEY='test2342sstsfgasjbdfhksbdfjhsgdkf'
ADMINS=None
SERVER_EMAIL=None
```
* Create two folders under `bingosync-app`. One named `logs` and one named `database`

To run the app, either run it from VSCode's launch options, or do the following:
* Source the virtual environment (if you haven't aready). Run `source venv/bin/activate`
* From `bingosync-app`, run `python manage.py migrate && python manage.py runserver`
* Open a 2nd terminal. From `bingosync-websocket`, run `python app.py`

Now you can access the site on `localhost:8080`