# Alpha: Go Launch a Survey!

### Building and Running
Install Go and ensure that your `GOPATH` env variable is set (usually it's `~/go`).

Note this app uses govendor (https://github.com/kardianos/govendor) to manage its dependencies.

```
go get -d github.com/ONSdigital/go-launch-a-survey/
cd $GOPATH/src/github.com/ONSdigital/go-launch-a-survey/
go get -u github.com/golang/dep/cmd/dep
$GOPATH/bin/dep ensure
go build
./go-launch-a-survey

go run launch.go (Does both the build and run cmd above)

```

Open http://localhost:8000/

### Docker
The dockerfile is a multistage dockerfile which can be built using:

```
docker build -t go-launch-a-survey:latest .
```

You can then run the image using `SURVEY_RUNNER_SCHEMA_URL` to point it at an instance of survey runner. 

```
docker run -e SURVEY_RUNNER_SCHEMA_URL=http://localhost:5000 -it -p 8000:8000 go-launch-a-survey:latest
```

The syntax for this will be slightly different on Mac

```
docker run -e SURVEY_RUNNER_SCHEMA_URL=http://docker.for.mac.host.internal:5000 -it -p 8000:8000 go-launch-a-survey:latest
```

You should then be able to access go launcher at `localhost:8000`

You can also run a Survey Register for launcher to load Schemas from 

```
docker run -it -p 8080:8080 onsdigital/eq-survey-register:simple-rest-api
```

### Run Quick-Launch
For this to work ensure the JSON you are passing has an eq_id and form_type.
Run Survey Launcher
```
scripts/run_app.sh
```
Now run Go launcher and navigate to "http://localhost:8000/quick-launch?url=" passing the url of the JSON
```
e.g."http://localhost:8000/quick-launch?url=http://localhost:7777/1_0001.json"
```

### Deployment with [Helm](https://helm.sh/)

To deploy this application with helm, you must have a kubernetes cluster already running.

You need to have Helm installed locally

1. Install Helm with `brew install kubernetes-helm` and then run `helm init --client-only`

1. Install Helm Tiller plugin for tillerless deploys `helm plugin install https://github.com/rimusz/helm-tiller`

To deploy to a cluster you can run the following command

```
helm tiller run helm upgrade --install survey-launcher ./helm/launcher --set surveyRunnerUrl=https://SURVEY-RUNNER-URL 
```

### Notes
* There are no unit tests yet
* JWT spec based on http://ons-schema-definitions.readthedocs.io/en/latest/jwt_profile.html

### Settings
Environment Variable | Meaning | Default
---------------------|---------|--------
GO_LAUNCH_A_SURVEY_LISTEN_HOST|Host address  to listen on|0.0.0.0
GO_LAUNCH_A_SURVEY_LISTEN_PORT|Host port to listen on|8000
SURVEY_RUNNER_URL|URL of Survey Runner to re-direct to when launching a survey|http://localhost:5000
SURVEY_REGISTER_URL|URL of eq-survey-register to load schema list from |http://localhost:8080
JWT_ENCRYPTION_KEY_PATH|Path to the JWT Encryption Key (PEM format)|jwt-test-keys/sdc-user-authentication-encryption-sr-public-key.pem
JWT_SIGNING_KEY_PATH|Path to the JWT Signing Key (PEM format)|jwt-test-keys/sdc-user-authentication-signing-rrm-private-key.pem
