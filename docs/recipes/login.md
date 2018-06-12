---
layout: guide
---

The easiest way to implement login is to have a list of users either in your database or in static json-file along with hashes of their passwords. This way you would need to perform a few steps.

First we need a form to ask user for login-data. A webchat renderer for it can look something like this:

```js
'#login-form': data => [
  { typing: true, markdown: true, type: 'login_prompt' }
],
```

Second - make sure that your `bp.hear` listens to `form` event types. E.g. like this:

```js
bp.hear({ type: /login_prompt|text|message|quick_reply/i }, (event, next) => { /* ... */ })
```

And finally you need an action that would compare login-data with records in JSON or in DB and set state variable indicating login status.

```js
async function login(state, { raw: { data } }) {
  const { username, password } = data

  // Could be replaced by fetching user from DB or static file
  const users = [
    {
      username: 'testuser',
      password: '$2b$10$xAwE6hDEZnu6wd8gMMOVf.OERQXLp96Ew/CVO4VM.RkEmVvzKdpya' // 'myPlaintextPassword'
    }
  ]
  const user = users.find(user => user.username === username)

  const isPasswordValid = await bcrypt.compare(password, user.password)
  return { ...state, username: isPasswordValid ? username : null }
}
```

Note, that in the example above we are relying on `bcrypt` to create password hash so it needs to be installed and imported before using.
