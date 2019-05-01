# User 


```sh
curl https://circleci.com/api/v1.1/me?circle-token=:token
```

```javascript
const CCI = new CircleCI({token: "A2i9dklaja...", vcsType: "github", username: "circleUser123"})

CCI.getUser()
  .then(data => console.log(data))
  .catch(err => console.log(err))
```

```json
{
  "basic_email_prefs" : "smart", // can be "smart", "none" or "all"
  "login" : "pbiggar" // your github username
}
```

**`GET` Request**: Provides information about the user that is currently signed in.


