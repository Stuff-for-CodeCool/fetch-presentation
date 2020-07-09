# AJAX using the Fetch API

`AJAX`, or `Asynchronous JavaScript and XML`, is a method of getting information via JavaScript and using it in a page _without refreshing the page_.

This is most commonly used in modern browsers using the `fetch API`. The `fetch API` returns a promise, and there are two methods of using this promise. We'll get into both of them.

## The setup

We shall use the [randomuser](https://randomuser.me/) API to generate fake users, and use [base.html](./base.html) as a template (it has no styling, but as that is not the focus of this presentation, styling it will be left as an exercise to the reader).

## fetch

`fetch()` is an API which returns a response from a URI; being built-in, it needs no other frameworks or libraries. It is currently the simplest way to use ajax calls; prior to this, `XMLHttpRequest` was the standard, and it can still be seen in legacy code. Now that we have `fetch`, though, we will use that.

**Very important:** `fetch` is currently **not** supported by Internet Explorer.

## Promises

`fetch` returns a promise. What this means is that the result will **not** be known until the promise is resolved. Attempting to use this data before the promise is resolved generally throws an error, as the data requested will innitially be undefined. Additionally, the promise may be rejected (in case of an error, say).

The standard way to use fetch is below, and can be seen in action on [promise.html](./promise.html):

```
function getUsers(count) {
    const list = document.querySelector('#list');

    fetch(`https://randomuser.me/api/?results=${count}`)
        .then(data => data.json())
        .then(data => {
            let results = data.results;
            for (let result of results) {
                list.innerHTML += populateCard(result);
            }
        })
        .catch(error => console.log(error));
}

getUsers(5);
```

The basic usage is as follows:

```
fetch(<API ENDPOINT HERE>)          // The URL you get information from / post information to
    .then(data => data.json())      //  Convert the result to JSON
    .then(data => callback)         //  Callback function, which uses the data object somehow
    .catch(error => errorCallback)  //  Do something with the errors (optional)
    .finally(error => callback)     //  This calllback always runs, regardless (optional)
```

Now, you will notice the use of `then`, twice. Should the application be more complex, this chain of `then` statements may get quite long, which impacts both readability and maintainability.

## Async / await

**Please note** that async/await are currently not supported in all browsers, so you might be better off using the previous method.

However, if your browser does support async/await, the code suddenly looks much cleaner, as can be seen on [async.html](./async.html):

```
async function getUsersAsync(count) {
    let response = await fetch(`https://randomuser.me/api/?results=${count}`);
    let data = await response.json();
    if (response.ok) {
        let results = data.results;
        for (let result of results) {
            list.innerHTML += populateCard(result);
        }
    }
}

getUsersAsync(5);
```

What to look out for:

-   the **async** keyword before the `function` keyword
-   the **await** keyword before any asynchronous function call

This is used twice in the example:

-   when fetching the user list
-   when converting the response to JSON

The rest of the function remains the same.

## POSTing data

Ok, so we've seen how to GET data. But what if we want to POST something?

Well, `fetch` has two arguments; the first one is the endpoint URL. The second one is a JavaScript object which describes what information you're sending.

It looks like this, with the default and possible values:

```
{
    method:         "GET",         // GET, POST, PUT, DELETE
    mode:           "cors",        // cors, no-cors, same-origin
    cache:          "default",     // default, no-cache, reload, force-cache, only-if-cached
    credentials:    "same-origin", // same-origin, include, omit
    headers: {
        "Content-Type": "application/json"  // 'Content-Type': 'application/x-www-form-urlencoded',
    },
    redirect:       "follow",       // follow, manual, error
    referrerPolicy: "client",       // client, no-referrer
    body: JSON.stringify(data)      // body data type must match "Content-Type" header
}
```

Should the `mode` be `cors`, only the following headers are accepted:

-   Accept
-   Accept-Language
-   Content-Language
-   Content-Type: `application/x-www-form-urlencoded`, `multipart/form-data`, or `text/plain`

To POST JSON data:

```

let dataToBePosted = {
    example: "This right here",
    test: "None defined"
}

fetch(endpointUrl, {
    method: "POST",
    headers: {
        "Content-Type": "application/json"
    },
    body: JSON.stringify(dataToBePosted)
})
.then(data => data.json())
.then(data => console.log('Success:', data))
```

For a taste of how this works with async / await, see [posting.html](./posting.html).

## Uploading a file

Files can be uploaded using an HTML `<input type="file" />` input element, `FormData()` and `fetch()`.

```
const formData = new FormData();
const fileField = document.querySelector('input[type="file"]');

formData.append('file', fileField.files[0]);

fetch(endpointUrl, {
  method: 'POST',
  body: formData
})
.then(data => data.json())
.then(data => console.log('Success:', data));
```
