# authentication-utilities

## Installation
```npm install --save authentication-utilities ```

## Usage
Exports three utility functions that carry out common authentication tasks

### authenticateAsync(URL, username, password)
Checks whether a username and password authenticate against a given URL.
Does this by querying the URL endpoint with a Basic auth header attached to the HTTP GET query.
Will return the response from the server interpreted as JSON, or throw an error if anything goes wrong.
Errors can be:
* Generated by the function - missing credentials, invalid URL, connection failure.
* Generated by the server - invalid username/password, permission denied, etc.
Errors generated by the server should be returned as JSON under the 'error' key.

```
import {authenticateAsync} from 'authentication-utilities';
import {gui} from './gui.js';

const AUTH_URL = 'https://favouritecolours.com/login';

async function login(username, password) {
  try {
    const responseJson = await authenticateAsync(AUTH_URL, username, password);
    const favouriteColour = responseJson.user.favouriteColour; // Contents of responseJson depends on what the server returns
    console.log('LOGGED IN!');
    gui.setBackgroundColour(favouriteColour);
  } catch (error) {
    console.log('FAILED TO LOGIN');
    console.log(error.message);
  }
}
```

### getAuthHeader(username, password)
Returns a Basic auth header with the given username and password in a Base64 encoded string

```
import { getAuthHeader } from 'authentication-utilities';

const COLOUR_UPDATE_URL = 'https://favouritecolours.com/user/favouriteColour?colour=';

function updateFavouriteColour(newFavouriteColour, username, password) {
  fetch(COLOUR_UPDATE_URL + newFavouriteColour, {
    method: 'POST',
    headers: {
      Authorization: getAuthHeader(username, password),
    },
  );
}
```

### hashPassword(password)
Encodes a given password using sha256 and optional salt, producing a non-reversible hash

```
import { hashPassword } from 'authentication-utilities';

class User {

  ...

  updatePassword(password) {
    this.encodedPassword = hashPassword(password, salt); // Store non-reversible hash of the password for security
  }

  ...

}
```
