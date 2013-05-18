#slim-jsonAPI
This is an extension to the [SLIM framework](https://github.com/codeguy/Slim) to implement json API's with great ease.

##instalation
Using composer you can add use this as your composer.json

```json
    {
        "require": {
            "slim/slim": "2.*",
            "entomb/slim-json-api": "dev-master"
        }
    }

```

##Usage
To include the middleware and view you just have to load them using the default _Slim_ way.

```php
    require 'vendor/autoload.php';

    $app = new \Slim\Slim();

    $app->view(new \JsonApiView());
    $app->add(new \JsonApiMiddleware());
```

after this, all your requests will be returning json output.

the usage will be `$app->render(HTTP_CODE, array DATA);`

```php

    $app->get('/', function() use ($app) {
        $app->render(200,array(
                'msg' => 'welcome to my API!',
            ));
    });

```
```json
{
    "msg":"welcome to my API!",
    "error":false,
    "status":200
}

```


To display an error just set the `error=>true` in your data array.
All requests have an `error` var and it default to false.
The HTTP code will also default to `200`

```php

    $app->get('/user/:id', function($id) use ($app) {

        //your code here

        $app->render(404,array(
                'error' => TRUE,
                'msg'   => 'user not found',
            ));
    });

```
```json
{
    "msg":"user not found",
    "error":true,
    "status":404
}

```


##middleware
The middleware will set some static routes for default requests.
if you dont want to use it, you can copy this code into your bootstrap file

```php

    // Mirrors the API request
    $app->get('/return', function() use ($app) {
        $app->render(200,array(
            'method'    => $app->request()->getMethod(),
            'name'      => $app->request()->get('name'),
            'headers'   => $app->request()->headers(),
            'params'    => $app->request()->params(),
        ));
    });

    // Generic error handler
    $app->error(function (Exception $e) use ($app) {
        $app->render(500,array(
            'error' => TRUE,
            'msg'   => $e->getMessage(),
        ));
    });

    // Not found handler (invalid routes, invalid method types)
    $app->notFound(function() use ($app) {
        $app->render(400,array(
            'error' => TRUE,
            'msg'   => 'Invalid route',
        ));
    });

    // Handle Empty response body
    $app->hook('slim.after.router', function () use ($app) {
        if (strlen($app->response()->body()) == 0) {
            $app->render(500,array(
                'error' => TRUE,
                'msg'   => 'Empty response',
            ));
        }
    });


```
