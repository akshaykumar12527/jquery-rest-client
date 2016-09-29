# CupCoffeeJs jQuery Rest Client

Minimal library for restful client based on jQuery Ajax. Include a semaphore that controls the execution of each session. The purpose of this library is to improve the integration with restfull server on the client side using jQuery Ajax based and a structure that allows simulate an asynchronous architecture.

## Install
`bower install jquery-rest-client`

`npm install jquery-rest-client`

## What you should know to use

This library is being built to work with CupCoffee Js, specifically Cupcoffee Auth JWT, but should work perfect mind in any structure, follow these tips: The communication paths generated here are these standards:

### Create / Insert / Post (method, value) [Method POST]

`[url_api] / [controller] / [method]`

### Read / Get (method) Method GET

`[url_api] / [controller] / [method] / [id]? Query = query & ...`

### Update (method, value) [Method Put]

`[url_api] / [controller] / [method] / [id]`

### Delete (method, value) [Method Delete]

`[url_api] / [controller] / [method] / [id]`

## Examples

```javascript
    var rest = new $.RestClient('http://localhost:3000')
        rest.add('login') // http://localhost:3000/login
        rest.add('categories', 'api/categories')  //http://localhost:3000/api/categories
        rest.add('comments', 'api/comments') //http://localhost:3000/api/comments
        rest.add('posts', 'api/posts') ////http://localhost:3000/api/posts
        rest.add('users', 'api/users') ////http://localhost:3000/api/users

        rest.login.post({
            'username': "admin",
            "password": "123456"
        }).then(function(data) {
            rest.token(data.token, data.data.id);// Save x-access-token and x-key
            rest.release('login');
        })

        rest.wait('login')

        /*
            None of the sessions below will be performed unless "rest.release ('login')" is executed.
         */

        rest.users.read('find').query({
            'id': '1'
        }).then(function(data) {
            rest.release('user');
            console.log(data)
        })

        rest.categories.wait('post').update('category').data({
            title: 'news',
            id: '3'
        }).then(function(data) {
            rest.comments.delete(3).then(function(data){
                if(data == true){
                    alert('Comment deleted!')
                }
            })
        })

        //to url/api/user/add, use action method
        rest.posts.wait('user').action('add').create({title: 'Hello', content: "Word!"}).then(function(data) {
            rest.release('posts')
        })
```
In this example requests the following order: Read User -> Create Post -> Update Category -> Delete comments

## Options

```json
{
    "waitingTime": 300, //Waiting time
    "waitLoopLimit": 60, //Quantity connection attempts limit
    "logger": 0, // Log display (0 and 1 = all, 2 = only errors)
    "fnError": function(msg) { //Called when an error is declared
        //...
     },
    "setup": {} //jQuery Ajax configuration http://api.jquery.com/jquery.ajax/
}
```

## Methods

- `.post, .insert and .create:` Send data in the body of the request using the POST method.
- `.remove, .delete, .del and .destroy:` Send a removal request with request to the url uses the DELETE method.
- `.update:` Sends data in the header of the body using the PUT method.
- `.read, .get:` Send a request to the URL using the GET method.
- `.custom:` Receives an object with all the settings for a model.
- `.isGet:` Sets the method to GET.
- `.isPost:` Sets the method to POST.
- `.isPut:` Sets the method to PUT.
- `.isDelete:` Sets the method to DELETE.
- `.method:` Defines a method to be used.
- `.headers:` Apply a header to this model.
- `.defaultHeaders:` Apply a standard header to all models.
- `.token:` Save x-access-token and x-key.
- `.then:` Execute and return the values in a callback, return $ .ajax.
- `.exec:` Run the command without callback, return $ .ajax.
- `.error:` Returns an error message for a callback.
- `setApi:` Defines the API address.
- `.wait:` Force the model or models below to wait the release of certain event.
- `.release:` Releases waits.
- `action:` Add values in the URL path to access methods (actions) server

If the method has not yet defined `.query()` is applied, the method will be set as "GET". If `.data()` is applied, the method will be set to "POST".
