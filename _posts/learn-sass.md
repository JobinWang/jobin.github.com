SCSS: Sassy CSS
@import 'name'

### Nesting
.content {
  border: 1px solid #ccc;
  padding: 20px;
  .callout {
    border-color: red;
  }
&.callout {
    border-color: green;
  }
}


.content {
  border: 1px solid #ccc;
  padding: 20px;
}
.content .callout {
  border-color: red;
}
.content.callout {
  border-color: green;
}

### Variable

$title: 'My Blog'
$title: 'About me' !default  # doesn't overwrite

-> #{$title}


Setting new values to variables set outside a declaration changes future instances




### Mixin

@mixin button {

}

{
@include button;
}

### Extend


### Directive

* Mixin: Similar sets of properties used multiple times with small variations.
* Extend: Sets of properties that match exactly
* Functions: Commonly-used operations to determine values


Here are a few useful links which are referenced in the course and video. Below, you can find your achievements and awards.


CodeKit http://incident57.com/codekit/
LiveReload http://livereload.com/
Middleman http://middlemanapp.com/
Scout http://mhs.github.com/scout-app/
The Sass Way http://thesassway.com/
Chris Eppstein's Blog http://chriseppstein.github.com/blog/
Kicking Ass + Taking Names with Sass & Compass by Nathan Henderson http://vimeo.com/24278115
CSS Tricks — Sass vs. Less http://css-tricks.com/sass-vs-less/
Compass website http://compass-style.org/
