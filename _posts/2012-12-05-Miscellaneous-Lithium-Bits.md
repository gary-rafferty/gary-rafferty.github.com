---
layout: single
title: Miscellaneous Lithium (li3) bits and bobs
categories: [software]
tags: [php lithium]
---
In work, we recently started development on a new product to solve the problems with Facebook Promoted Posts.

Before we started the actual dev, we were weighing up either sticking with our inhouse framework, or bringing
in something new. We evaluated the usual suspects of Symfony2, Zend, and CakePHP, but none of these frameworks
cut it for one reason or another. Long story short, we were pretty impressed with [Lithium](http://lithify.me), otherwise known as li3, 
and have been getting good results. 

My only issue with Lithium is the lack of resources, other than a handful of StackOverflow questions and the 
[manual](http://lithify.me/docs/manual). Though as usage increases, the amount of resources will too increase.
Here's some random things that took a few minutes to find out.

### Render an element to a view

Elements can simply be thought of as reusable view components. In the Rails world, we call them partials :)
In Lithium, you can render an element by storing it in app/views/elements/myelement.html.php and then calling
the element directly from the view.

```php
<?php echo $this->_render('element','myelement')?>
```

The Rails alternative would be slightly less verbose but much the same

```ruby
  <%= render 'myelement' %>
```

### Have a controller respond with JSON

Nothing major, but it took me a few minutes to figure this one out.

```php
  <?php echo $this->render(array('json' => $some_response)) ?>
```

### Validations

The model validations aren't quite as nice as they are in Rails, but they do the trick.
Here's a sample, and check out the Validations section in the manual for more information.

```php
<?php
class MyModel extends \lithium\data\Model {
  public $validates = array(
    'attribute' => array(
      array(
        'notEmpty',
        'message' => 'Attribute cannot be empty'
      ), array(
        'inRange',
        'lower' => 0,
        'upper' => 5,
        'message' => 'Attribute must be within 1 and 4'
      )
    )
  );

  //... rest of class def...
}
?>
```

The only pain in the arse with the inRange validator is that it is not inclusive, so your lower 
value must be 1 below the lowest valid value, and the upper must be 1 higher. In the above example, 
valid values are 1,2,3, and 4. Just took a failing unit test to cop that one.

### Model associations

When you are using a relational datastore, then you can make use of model relationship helpers.
These do not work in MongoDB or other NoSQL stores.

```php
<?php
class MyModel extends \lithium\data\Model {
  public $hasOne = array('Account');
  public $belongsTo = array('User');
  public $hasMany = array('Friends');
}
?>
```

I'll add some more to the list of miscellaneous Lithium bits and bobs when I get a chance.
