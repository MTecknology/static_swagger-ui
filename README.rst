Static Swagger-UI
=================

This script takes the same JSON file that Swagger-UI requires and builds a
static web page with a nearly identical appearance to what is produced by
Swagger-UI.

Benefits:

- Browser does not need to download and parse a large JSON file
- Browser does not need to keep the large file in memory
- No wait for initial download and rendering "Loading..."
- No wait for expanding sections
- No additional processing to build sections before expanding
- Browser does not need to handle heavy javascript processing
- No dependencies outside of base python
- Written for Debian Devs; easily used in any project deployment
- Works as a python module

This script does not implement all features of the javascript version
and instead focuses on providing a useful manual.

Development Status
------------------

Unless interest develops, I do not intend to actively maintain this project.

Testing
-------

Local environment variables::

    export SWAGGER_DST='swaggerui.html'
    export SWAGGER_SRC='swagger.v1.json'

Get JSON::

    python -c 'import build; build.download_json()'

Build::

    ./build.py

Debian Dev
----------

This script exists to avoid packaging an excessive and insane number of
build dependencies for node-swagger-ui. The additional benefits are just
happy side effects.

To add this to your package:

- debian/control: Build-Depends: python
- ``wget https://raw.githubusercontent.com/MTecknology/static_swagger-ui/master/build.py -O debian/helpers/swagger_build.py``
- ``sed -i '/REMOVE/,/REMOVE/d' debian/helpers/swagger_build.py``
- d/rules: override_dh_auto_build: [...]

Example d/rules::

    override_dh_auto_build:
            python debian/helpers/swagger_build.py
            # or..
            SWAGGER_SRC='/.../swagger.v1.json' SWAGGER_DST=swag.html python debian/helpers/swagger_build.py
            go install -v -p 4 \
                    -buildmode=pie \
                    -pkgdir="$(GOPATH)" [...]

Note: The license on this source is now Apache-2.0 which only requires
attribution in source. (i.e. maintain the file header; that's it)

Module
------

This script can be used in python as a module.

An overly-verbose example::

    import build as swag
    tmp = 'swag.json'
    output = 'swagger-static.html'
    swag.download_json('http://domain.tld/swagger.v1.json', tmp)
    json_data = swag.read_json(tmp)
    rendered_page = swag.render_page(json_data)
    write_successe = swag.write_static(rendered_page, outpt)
    if not write_success:
        print ('oops...')

More simply::

    import build as swag
    src = '/.../swagger.v1.json'
    page = swag.render_page(swag.read_json(src))


Shell
-----

Of course, it's also possible to run it from a shell::

    # download
    SWAGGER_URL=http://domain.tld/swagger.v1.json SWAGGER_DST=swagger.v1.json python build.py
    # or build
    SWAGGER_SRC=swagger.v1.json NODL=1 SWAGGER_DST=public/swagger.html python build.py
    # or both
    SWAGGER_URL=http://domain.tld/swagger.v1.json SWAGGER_DST=swag.html python build.py

Demo
----

Execution::

    python build.py

Output:

- swagger.v1.json (download from https://try.gitea.io/swagger.v1.json)
- swagger.html (pretty static web page)

Environment Variables:

- SWAGGER_SRC: JSON input file (default: swagger.html)
- SWAGGER_DST: Static HTML file; Download destination location (default: swagger.v1.json)
- SWAGGER_URL: URL to download JSON file from (default: https://try.gitea.io/swagger.v1.json)
- NODL: Skip download check; or see above sed command (default: <unset>)
