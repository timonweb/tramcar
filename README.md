# Welcome!

Tramcar is a _multi-site_, _self-hosted_ __job board__ built using
[Django](https://www.djangoproject.com/).  This project is still in its infancy,
but we welcome your involvement to help us get Tramcar ready for production
installs.

## Features

* Host multiple job boards on the same instance using Django's "sites" framework
* Allow free or paid job postings, with paid postings using Stripe Checkout for payment processing
* Automatically tweet job details when a post is activated
* Automatically expire jobs after an admin-defined period
* E-mail notifications alert the admin when a post is made and the job owner when their job has expired
* Job posts support Markdown for creating rich text descriptions and application information

## Installation

First, clone and install dependencies.  This requires python 3.5, pip, and
virtualenv to already be installed.

```
$ git clone https://github.com/wfhio/tramcar
$ cd tramcar
$ virtualenv .venv
$ source .venv/bin/activate
(.venv) $ pip install -r requirements.txt
```

Tramcar defaults `SECRET_KEY` in `tramcar/settings.py` to an empty string
which will prevent Django from starting up.  This is done to ensure that
deployers do not accidentally deploy with a default value.  Before proceeding,
set a unique value for `SECRET_KEY` in `tramcar/settings.py`.  If you have `pwgen`
installed, simply do this:

```
$ PWD=$(pwgen -s 50 1)
$ sed -i.bak "s/^SECRET_KEY = ''$/SECRET_KEY = '$PWD'/g" tramcar/settings.py
```

Now, apply database migrations, create an admin user, and start the
development server:

```
(.venv) $ python manage.py migrate
(.venv) $ python manage.py createsuperuser
Username (leave blank to use 'root'): admin
Email address: admin@tramcar.org
Password:
Password (again):
Superuser created successfully.
```

The default site has a domain of `example.com`, this will need to be changed to
`localhost` for development testing or to whatever live domain you will be
using in production.  For example, to test Tramcar locally, issue the following
command:

```
(.venv) $ sqlite3 db.sqlite3 "UPDATE django_site SET domain='localhost' WHERE name='example.com';"
```

## Fixtures

We have a fixtures file in `job_board/fixtures/countries.json`, which you can
load into your database by running the following:

```
(.venv) $ python manage.py loaddata countries.json
```

This will save you having to import your own list of countries.  However, be
aware that any changes made to the `job_board_country` table will be lost if
you re-run the above.

## Start Tramcar

To run Tramcar in a development environment, you can now start it using the
light-weight development web server:

```
(.venv) $ python manage.py runserver
```

To run Tramcar using Apache2 and mod_wsgi, see the
[following](https://github.com/wfhio/tramcar/wiki/Production-Deployment-Notes)
for more information.

## Final Steps

At this point, Tramcar should be up and running and ready to be used.  Before
you can create a company and job, log into <http://localhost:8000/admin> using
the username and password defined above.  Once in, click Categories under
JOB_BOARD and add an appropriate category for the localhost site.

That's it!  With those steps completed, you can now browse
<http://localhost:8000> to create a new company, and then post a job with that
newly created company.

(If deploying with a non-localhost domain, replace `localhost` above with
the domain you are using)

## Job Expiration

Jobs can be expired manually by logging in as an admin user and then clicking
the `Expire` button under `Job Admin` when viewing a given job.  A simpler
solution is to run this instead:

```
(.venv) $ python manage.py expire
```

The above will scan through all jobs across all sites and expire out any jobs
older than the site's `expire_after` value.  Ideally, the above should be
scheduled with cron so that jobs are expired in a consistent manner.

## Support

Found a bug or need help with installation?  Please feel free to create an [issue](https://github.com/wfhio/tramcar/issues/new) or drop into [Slack](http://tramcar.slack.com/) and we will assist as soon as possible.
