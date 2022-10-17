# To Contribute a Rust Assignment
* All assignments in this repository are licensed [CC BY-SA 4.0](http://creativecommons.org/licenses/by-sa/4.0/?ref=chooser-v1). 
Make sure you are comfortable with those license requirements before submitting an assignment.
* Fork the [repository](https://github.com/rust-edu/assignments). To create a new fork:
  * Find the `Fork` drop-down menu in the upper-right area of the repository page.
  * Click on the menu and select `Create a new fork`.
* Add your assignment to the [`_posts`](https://github.com/rust-edu/assignments/tree/main/_posts) folder.
  * The assignment should be in [`md`](https://www.markdownguide.org/) format, with a header as described below.
* Its filename should be in the following format:
  * `YYYY-MM-DD-Title`
  * For `YYYY-MM-DD`, use the current date.
  * Make sure that `Title` is consistent with the title of the assignment in the body of its text.
* Once your assignment is ready, make a pull request to contribute it. To make a pull request:
  * Go to your `github` fork of the repository.
  * Find the `Contribute` drop-down menu.
  * Click on the green button labeled `Open pull request`.

## Header

Below is an example header to use as a model:

```
---
tags:
- Operating Systems
- Easy
---
```

Add whatever tags you consider appropriate for your assignment. If you would like to suggest a new tag, please 
[post an issue](https://github.com/rust-edu/assignments/issues). Current tags include:
<ul>
{% for tag in site.tags %}
  <li> {{ tag | first }} </li>
{% endfor %}
</ul>
