# go-docker
Basic model for go (golang) application development using docker.

Mainly for microservices web apps.

## Usage
You should place your source code under src directory. In fact, there is a basic "Hello World" app in that directory as a sample.

To create a docker development environment (obviously you must have docker installed), cd to go-docker folder and run:

docker build -t go-docker-dev -f Dockerfile.dev .

That will create a docker image containing golang, dep (to manage go dependencies), and gin as a "live reloading" tool. 

Now you can run the container, start a shell, and run the application:

docker run --rm -it -p 8080:8080 -v $(pwd):/go/src/app go-docker-dev bash

    root@297a30162492:/go/src/app# dep ensure --vendor-only
    root@297a30162492:/go/src/app# go run src/main.go
    INFO[0000] Listening on port 8080...

Test with your browser at http://localhost:8080.

Exit the container's shell. Kill it with "Ctrl + C".

There'll be a new "vendor" directory containing the app's dependecies.

You can now start the container enabling live reloading with:

docker run --rm -it -p 8080:8080 -v $(pwd):/go/src/app go-docker-dev gin --path src --port 8080 run src/main.go

Try modifying the source code and see the changes applied inmediately.

Now you can create a "production" image version using a multi stage build with Dockerfile.prod:

docker build -t go-docker-prod -f Dockerfile.prod .

And run it with:

docker run --rm -it -p 8080:8080 -v $(pwd):/go/src/app go-docker-prod

Note that the production image is much smaller than the development one.

If you need a *really* light image, you can use Dockerfile.prod.minimum file to generate a "compressed" binary version of your application:

docker build -t go-docker-prod-min -f Dockerfile.prod.minimum .

And run it:

docker run --rm -it -p 8080:8080 -v $(pwd):/go/src/app go-docker-prod-min