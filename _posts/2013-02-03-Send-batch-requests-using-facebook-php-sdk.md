---
layout: single
title: Send batch requests to Graph API using Facebook's PHP client
categories: [software]
tags: [php facebook]
---
The PHP SDK provided by Facebook is quite good, though it lacks the ability to send [batched requests](https://developers.facebook.com/docs/reference/api/batch/).
The reason for sending batched requests is that it allows you to combine multiple requests into a single one.

I've added the functionality and submitted a [pull request](https://github.com/facebook/facebook-php-sdk/pull/62) on GitHub, but just incase that doesn't get merged for some reason, here's how to do it yourself.

Modify `src/base_facebook.php` and add the following function

```php
<?php
  /*
   * Send a batched request to the graph api
   * @return retval - The response body
   */
  function batch($data) {

    $endpoint = self::$DOMAIN_MAP['graph'];

    $payload = json_encode($data);

    $post_data = array(
      'access_token' => $this->getAccessToken(),
      'batch' => $payload
    );

    $responses = json_decode($this->makeRequest($endpoint,$post_data),true);

    $retval = array();

    foreach($responses as $response) {
      $retval[] = json_decode($response['body'],true);
    }

    return $retval;
  }
?>
```

And then be a nice guy, and add a quick test to tests/tests.php

```php
<?php
  public function testBatchedAPIGraphCall() {
    $facebook = new TransientFacebook(array(
      'appId'  => self::APP_ID,
      'secret' => self::SECRET
    ));

    $batch_data = array(
      array('method' => 'GET', 'relative_url' => 4),
      array('method' => 'GET', 'relative_url' => 5)
    );

    $response = $facebook->batch($batch_data);
    $this->assertEquals(2, count($response));
    $this->assertEquals(4, $response[0]['id']);
    $this->assertEquals(5, $response[1]['id']);
    $this->assertEquals('zuck', $response[0]['username']);
    $this->assertEquals('ChrisHughes', $response[1]['username']);
  }
?>
```

You can see the usage in the test above, but essentially all that you need to do is create a multidimensional array where each value 
is an array containing the method type, and the relative url. The just call the ->batch() method on the Facebook instance and parse the results.
