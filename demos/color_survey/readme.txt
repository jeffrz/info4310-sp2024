As this program uses a database, there is a bit more initial setup involved.
 You'll need Postgres installed locally so that you can host the database when you are developing
 You will also need to get a database running on Render to store information. Render offers a free database that expires after 90 days

*** First, install Postgres (https://www.robinwieruch.de/postgres-sql-macos-setup is a reasonable guide) and run initdb

* Make sure you have the Python libraries we will need (check requirements.txt). Use PIP to install any missing ones
 (If you've been following along, you will need SQLAlchemy, flask_SQLAlchemy, flask_compress, psycopg2 )

* Check to make sure your postgres db service is running by following a postgres tutorial

* You may need to set up an environment variable to tell SQLAlchemy where to find your database
  If you configured things using default settings, the following environment commands should work
  CAUTION: if you already use SQLAlchemy, this may wipe important settings
In a command line:
 for Mac: export SQLALCHEMY_DATABASE_URI=postgres://localhost
 for Win: SET SQLALCHEMY_DATABASE_URI=postgresql://localhost


*** Code up your app.py

* Make your data model and endpoints.

* In our app.py we will have to add a bit of code to make sure that the table has been properly created on the server. Paste in this code right underneath your database class:

  engine = db.create_engine(SQLALCHEMY_DATABASE_URI)
  inspector = db.inspect(engine)
  if not inspector.has_table("colorData"):
    with app.app_context():
        # db.drop_all()  # DANGER: Only include this line if you want to delete ALL existing tables
        db.create_all()
        app.logger.info('Initialized the database!')
  else:
    app.logger.info('Database already contains the colorData table.')

* Now test app.py locally and see if it works as expected. The first time you run it the new colorData table should be created!


*** Now we need to do the same thing on Render

* First, create a new PostgreSQL database on Render. I suggest naming the database and user name something that you will remember. Once it is created, you should see several masked strings on the status page. We need to copy the "Internal Database URL" so that we can store it on the server as an environment variable. 

* Next, create a new Web Service project on Render and link it to a repository. Make sure you use a Python 3. Set the startup command to be gunicorn app:app so that it figures out to use Flask. Go into your web service settings. Click Environment and the add an Environment Variable. Set its name to "DATABASE_URL" and paste in the internal data URL from the Postgres service so your server can use it. Save changes.



* Finally, commit your code and update the server so that it will run. Check for errors.



***** HELPFUL LINKS *****
-Guides some of the lesson was based upon
https://testdriven.io/blog/flask-render-deployment/
https://render.com/docs/deploy-flask

-Installing Postgres
Intel Mac
https://chartio.com/resources/tutorials/how-to-start-postgresql-server-on-mac-os-x/

M1 Mac
https://sqlpad.io/tutorial/postgres-mac-installation
https://gist.github.com/phortuin/2fe698b6c741fd84357cec84219c6667

Windows
https://wiki.postgresql.org/wiki/Homebrew
https://www.datacamp.com/tutorial/installing-postgresql-windows-macosx

-Postgres + Flask overviews
https://realpython.com/flask-by-example-part-2-postgres-sqlalchemy-and-alembic/
https://www.digitalocean.com/community/tutorials/how-to-use-a-postgresql-database-in-a-flask-application

-SQLAlchemy
https://docs.sqlalchemy.org/en/14/faq/index.html
