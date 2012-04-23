UNDER CONSTRUCTION
=========================

Heroku buildpack: OSGi (with Pax Runner)
=========================

This is a [Heroku buildpack](http://devcenter.heroku.com/articles/buildpack) for OSGi apps.
It uses Pax Runner 1.7.6 start the OSGi framework of choice and uses OpenJDK 1.6.0_20 to run it.

Usage
-----

Example usage:

    $ ls
    paxrunner

    $ heroku create --stack cedar --buildpack http://github.com/dennisg/heroku-buildpack-paxrunner.git
    
    $ git push heroku master
    ...
	-----> Heroku receiving push
	-----> Fetching custom buildpack... done
	-----> Java (using Pax Runner 1.7.6) app detected
	-----> Installing Pax Runner 1.7.6.....done!
	-----> Discovering process types
    Procfile declares types -> web
	-----> Compiled slug size is 5.6MB
	-----> Launching... done, v5
    http://<your-app-name>.herokuapp.com deployed to Heroku

The buildpack will detect your app as OSGi (using Pax Runner) if it has the file 'paxrunner' in the root. The file can be totally empty, it is just a marker file.

The Pax Runner distribution is unpacked inside your slug directory in '.buildpack'. A Procfile is not required, because the buildpack supplies a (I think) reasonable default.

Default Procfile:

	web: pax-run.sh --platform=${OSGI_FW} --version=${OSGI_FW_VER} --console=false --vmo="-Dorg.osgi.service.http.port=$PORT" $PROVISIONING_URL
	
where _OSGI_FW=felix_, OSGI_FW_VER_=4.0.2_, PORT supplied by Heroku and _PROVISIONING_URL=scandir:bundles_. This means that the default setup will start up an OSGi framework based on Felix, version 4.0.2 starting all bundles in 'bundles'.

Modifying the default OSGi framework is as simple as this:

	heroku config:add OSGI_FW=equinox
	heroku config:add OSGI_FW_VER=3.7.0


Provisioning URL

You can also simply change the provisioning url that is required for your slug

	heroku config:add PROVISIONING_URL=scandir:data
	
For more info on the various forms of provisioning urls: [Pax Runner Docs](http://team.ops4j.org/wiki/display/paxrunner/User+guide)
	
Example Custom Procfile:

	web: pax-run.sh --platform=equinox --console=false --vmo="-Dorg.osgi.service.http.port=$PORT" $PROVISIONING_URL


Hacking
-------

To use a modification of this buildpack, fork it on Github. Push up changes to your fork, then create a test app with `--buildpack <your-github-url>` and push to it too.
