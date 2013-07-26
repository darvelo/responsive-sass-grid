## Intro

This is a fluid responsive grid framework written purely in SASS. By `@import`ing it
into your SCSS, you gain a 12-column grid by default which is similar to what
you might get with Twitter Bootstrap. The difference is that you not only
get customizable CSS classes such as `.row`, `.col-3`, and `.col-offset-3`,
but you also get mixins which enable you to take the presentational CSS classes
out of your HTML so that everything becomes easier to maintain.

You can define grids of any size, and alter variables for any row, such as
total columns available, width of usable space in the row, the total width
of gutters, and the media query breakpoint below which columns will stack
at 100% width, useful for mobile devices.

[You can see how the grid works here](http://davidarvelo.com/e/sass-grid/),
or by checking out the `test` folder.  Resize your browser window and play
around with it.


## Presentational CSS Classes

You can choose to use the editable, generated CSS classes in your HTML if you like.
There are variables you can use to disable or alter them as needed. The SCSS will
generate `n` numbered classes for columns as specified by `$responsive-total-columns`,
and `n-1` classes for column offsets. In other words, if you specify a 10-column grid,
you'll get `.col-1` all the way to `.col-10`, and `.col-offset-1` to `.col-offset-9`.

```SCSS
// defaults
$responsive-classes-enable: true !default;
$responsive-row-class: 'row' !default; // ex. <ul class="row">
$responsive-column-class-prefix: 'col' !default; // ex. <li class="col-12">
$responsive-column-offset-class-prefix: 'col-offset' !default; // ex. <li class="col-5 col-offset-3">
```


## Global Variables

```SCSS
$responsive-total-columns: 12 !default;
$responsive-row-width: 100% !default;
$responsive-total-gutters-width: 20% !default;
$responsive-media-col-expand: 768px !default;
```

#### `$responsive-total-columns`
Default: `12`

Defines the number of columns a row can contain. This enables you
to define grids that can space any number of elements either equally or
disproportionately. For instance, five equal-width elements wouldn't
space evenly in a 12-column grid. But they could in a 10-column grid.
As a global variable, `12` is a flexible default.

#### `$responsive-row-width`
Default: `100%`

Defines the width of the row that will be used to calculate the
widths and gutters of the columns. Even though a row takes up
100% of its container, this variable effectively limits the total
width all combined columns and gutters will take inside the row.

#### `$responsive-total-gutters-width`
Default: `20%`

Defines the total width of the `$responsive-row-width` that will be taken
up by gutters, the left margins of columns. The number is a percentage cut
into the `$responsive-row-width`, so `($row-width * $total-gutters-width)`,
and represents the total width of `n-1` gutters, where `n` is the number
of columns. Column widths are calculated from the space left over after
total gutter width is calculated.

#### `$responsive-media-col-expand`
Default: `768px`

Defines the media query breakpoint at which columns will expand from stacked,
100% width blocks into grid-based columns in a row.


## Mixins

Mixins are preferred over presentational CSS classes in your HTML,
and give you much more power, flexibility, and clarity over your design.

There are three mixins available:

* `row`: Describes an element whose children are part of a column grid.
* `col-span`: Describes an element whose width will span a number of columns.
* `col-offset`: Describes an element whose left margin will span a number of columns.


### Mixin Variables

Every time you include a mixin, there are a number of variables
that you can pass in to alter how the grid will be displayed.
The variables available depend on the mixin.

All mixins take the optional arguments:

* `$expand`
* `$row-width`
* `$total-gutters-width`
* `$total-cols`

##### `$expand`
Default: `$responsive-media-col-expand`

#### `$row-width`
Default: `$responsive-row-width`

#### `$total-gutters-width`
Default: `$responsive-total-gutters-width`

#### `$total-cols`
Default: `$responsive-total-columns`

The `col-span` and `col-offset` mixins are special in that they
also take `$span-size` and `$offset-size` arguments, respectively.

#### `$span-size`

The amount of columns the element should expand to.

#### `$offset-size

The amount of columns the left margin of the element should expand to.

Please keep in mind that specified arguments should be applied
to a row and all its children. For instance, if you define
`$total-cols: 10` in a `row` mixin, you also need to define it for
direct descendents of that row which include the `col-span` or `col-offset`
mixin. This ensures that all margins and widths are calculated correctly.
Here's an example:

```HTML
<div class="my-row-class">
  <div class="left-block">...</div>
  <div class="right-block">...</div>
</div>
```

```SCSS
.my-row-class {
  @include row($total-cols: 10);
}

.left-block {
  @include col-span(4, $total-cols: 10);
}

.right-block {
  @include col-span(4, $total-cols: 10);
  @include col-offset(2, $total-cols: 10);
}
```

## Examples

You can get equal-width columns by doing:

```HTML
<div class="content">
  <div class="description-left">...</div>
  <div class="description-right">...</div>
</div>
```

```SCSS
.content {
  @include row;
}

.description-left, .description-right {
  @include col-span(6);
}
```

Or you can do something funky with:

```SCSS
  .description-left {
    @include col-span(3);
  }

  .description-right {
    @include col-span(3);
    @include col-offset(3);
  }
```

You can even adjust the collapsing/expanding breakpoint for this row manually with:

```SCSS
.content {
  // also available as `@include row($expand: 500px);`
  @include row(500px);
}

.description-left {
  // also available as:
  //   `@include col-span(3, $expand: 500px);`
  //   `@include col-span($span-size: 3, $expand: 500px);`
  @include col-span(3, 500px);
}

.description-right {
  @include col-span(3, 500px);

  // also available as:
  //   `@include col-offset(3, $expand: 500px);`
  //   `@include col-offset($offset-size: 3, $expand: 500px);`
  @include col-offset(3, 500px);
}
```

Or adjust the breakpoint globally by setting the
proper variable before including this module with SASS:

```SCSS
$responsive-media-col-expand: 500px;
@import 'responsive';
```

You can define a custom amount of columns for a specific row with:

```SCSS
.content {
 // this tells the mixin to make the row
 // divide its space into only 10 columns
  @include row($total-cols: 10);
}

.description-left, description-right {
  // this tells the mixin to make each description
  // element 2 columns wide in a 10-column grid
  @include col-span(2, $total-cols: 10);
}
```

Of course `@include col-offset(2, $total-cols:10);`
will create a 2-column offset in a 10-column space.
Since this is a one-time declaration, you can expect
other elements with the `row` and `col-span` mixins
that do not specify a `$total-cols` value to be spaced
according to the default column number, defined in
the `$responsive-total-columns` global variable.

A 15-column grid with three 5-column-width blocks, all
spaced to take up 50% of the row in total:

```HTML
<div class="half-width">
  <div class="three-times">5-col -- 50% row width</div>
  <div class="three-times">5-col -- 50% row width</div>
  <div class="three-times">5-col -- 50% row width</div>
</div>
```

```SCSS
.half-width {
  @include row($total-cols: 15, $row-width: 50%);

  .three-times {
    @include col-span(5, $total-cols: 15, $row-width: 50%);

    // you can also `float: right;` to take up the right 50%
    @media screen and (min-width: $responsive-media-col-expand) {
      float: right;
    }
  }
}
```

Total gutter width of 50%, leaving the total column widths spaced to 50%:

```HTML
<div class="gutter-large">
  <div class="size-1">50% total gutter</div>
  <div class="size-1">50% total gutter</div>
  <div class="size-1">50% total gutter</div>
  <div class="size-1">50% total gutter</div>
  <div class="size-1">50% total gutter</div>
  <div class="size-1">50% total gutter</div>
  <div class="size-1">50% total gutter</div>
  <div class="size-1">50% total gutter</div>
  <div class="size-1">50% total gutter</div>
  <div class="size-1">50% total gutter</div>
  <div class="size-1">50% total gutter</div>
  <div class="size-1">50% total gutter</div>
</div>
```

```SCSS
.gutter-large {
  @include row($total-gutters-width: 50%);

  .size-1 {
    @include col-span(1, $total-gutters-width: 50%);
  }
}
```

Remember that the total gutter width is based on the number of *total
columns,* not the number of col-spans your elements take up. This means that
if you create an element with `col-span(5, $total-gutters-width: 50%)`,
that element is taking up a significant portion of the total gutter width
that was proportioned out based on there being 12 columns total. Check the
tests under the header `Test Variable Total Gutter Width` to see examples.
