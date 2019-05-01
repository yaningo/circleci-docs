# Environment Variables

## List Environment Variables

```sh 
curl https://circleci.com/api/v1.1/project/:vcs-type/:username/:project/envvar?circle-token=:token
```

```javascript
const CCI = new CircleCI({token: "A2i9dklaja...", vcsType: "github", username: "circleUser123"})

CCI.listEnvVars("my-project")
  .then(data => console.log(data))
  .catch(err => console.log(err))
```


```json 
[{"name":"foo","value":"xxxx1234"}]
```

**`GET` Request**: Returns four 'x' characters plus the last four ASCII characters of the value, consistent with the display of environment variable values in the CircleCI website.

## Add Environment Variables

```sh 
curl -X POST --header "Content-Type: application/json" -d '{"name":"foo", "value":"bar"}' https://circleci.com/api/v1.1/project/:vcs-type/:username/:project/envvar?circle-token=:token
```

```javascript
const CCI = new CircleCI({token: "A2i9dklaja...", vcsType: "github", username: "circleUser123"})

CCI.createEnvVar("my-project", "VAR_NAME", "VAR_VALUE")
  .then(data => console.log(data))
  .catch(err => console.log(err))
```

```json
{"name":"foo","value":"xxxx"}
```

**`POST` Request** Creates a new environment variable.

## Get Single Environment Variable

```sh 
curl https://circleci.com/api/v1.1/project/:vcs-type/:username/:project/envvar/:name?circle-token=:token
```

```javascript
const CCI = new CircleCI({token: "A2i9dklaja...", vcsType: "github", username: "circleUser123"})

CCI.getEnvVar("my-project", "VAR_NAME")
  .then(data => console.log(data))
  .catch(err => console.log(err))
```

```json
{"name":"foo","value":"xxxx"}
```

**`GET` Request:** Returns the hidden value of environment variable `:name`.

## Delete Environment Variables

```sh
curl -X DELETE https://circleci.com/api/v1.1/project/:vcs-type/:username/:project/envvar/:name?circle-token=:token
```


```javascript
const CCI = new CircleCI({token: "A2i9dklaja...", vcsType: "github", username: "circleUser123"})

CCI.deleteEnvVar("my-project", "VAR_NAME")
  .then(data => console.log(data))
  .catch(err => console.log(err))
```

```json
{"message":"ok"}
```

**`DELETE` Request** Deletes the environment variable named :name.
