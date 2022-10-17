---
layout: default
---


# Contributing Rust Assignments
* All assignments in this repository are licensed [CC BY-SA 4.0](http://creativecommons.org/licenses/by-sa/4.0/?ref=chooser-v1). 
Make sure you are comfortable with those license requirements before submitting an assignment.
* Fork the repository.
* Add your assignment to the `_posts` folder.
  * The assignment should be in `md` format, with a header as described below.
* Its filename should be in the following format:
  * `YYYY-MM-DD-Title`
  * For `YYYY-MM-DD`, use the current date.
  * Make sure that `Title` is consistent with the title of the assignment in the body of its text.
* Once your assignment is ready, make a pull request to contribute it.

## Header

Below is an example header to use as a model:

```
---
layout: work
worktitle: Creating Shell Commands Using Rust
tags:
- Operating Systems
- Easy
---
```

* Replace the `worktitle` value with the title of your assignment.
* Add whatever tags you consider appropriate for your assignment. If you would like to suggest a new tag, please post an Issue. Current tags include:
<ul>
{% for tag in site.tags %}
  <li> {{ tag | first }} </li>
{% endfor %}
</ul>

<p xmlns:cc="http://creativecommons.org/ns#" >This work is licensed under <a href="http://creativecommons.org/licenses/by-sa/4.0/?ref=chooser-v1" target="_blank" rel="license noopener noreferrer" style="display:inline-block;">CC BY-SA 4.0<img style="height:22px!important;margin-left:3px;vertical-align:text-bottom;" src="https://mirrors.creativecommons.org/presskit/icons/cc.svg?ref=chooser-v1"><img style="height:22px!important;margin-left:3px;vertical-align:text-bottom;" src="https://mirrors.creativecommons.org/presskit/icons/by.svg?ref=chooser-v1"><img style="height:22px!important;margin-left:3px;vertical-align:text-bottom;" src="https://mirrors.creativecommons.org/presskit/icons/sa.svg?ref=chooser-v1"></a></p>