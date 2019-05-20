This is a starter theme for slate.

This repository is just for illustration and should only be used for reference. Feel free to fork and use it.

Please refer to slate documentation for basic usage.

slate has two versions:

The newer version has docs at V1 (this theme uses this version):

https://github.com/Shopify/slate/wiki


The older version has a diferent set of docs at V0 (useful information totally different set of files though):

https://shopify.github.io/slate/docs/0.14.0/

This theme can be used with slate by following this document:

https://shopify.github.io/slate/docs/starter-themes

it should work by creating a parent directory in you terminal entering:

yarn create slate-theme your-project-name danyn/theme-for-slate

or

npx create-slate-theme your-project-name danyn/theme-for-slate

Reason for this Repository:

There seems to be an issue with the product.js file not working for products without variants.  You can read the comments in the file that defines the class in node modules as that seems to be the only documentation on the actual workings of this by looking at the includes in scripts/sections/product.js.

The files with code examples or changes are:

templates/product

templates/product.no-variant

sections/product

section/product_no-variant


I had inteded to add more comments the code, that may be a good idea to get a better idea what is going on.

This version of starter theme addresses this by illustrating how you could create one template to handle products without variants and another template to handle all products whether or not they have variants by using a liquid conditional to bypass the intended js functionality and have it work just in liquid. The js still works when there are variants.

Alternately it might be worth at least understanding older working version of js for the products like is described here:
https://help.shopify.com/en/themes/development/templates/product-liquid
https://help.shopify.com/en/themes/customization/products/variants/use-products-with-multiple-options

Also:

** all products even ones that have no variants do have at least one 'variant' as the variant object contains more and different properties than the product object

the product object contains all the page level stuff and is also a reference to the variant object:

the variant object contains all the important stuff including the id of the the 'product' ie 'variant' that must be inside the product form in order for the form to submit and this the nature of the error we are seeing.

https://help.shopify.com/en/themes/liquid/objects/product

https://help.shopify.com/en/themes/liquid/objects/variant


try comparing these objects to get a sense of what is going on


So either of these would work:
```
<input type="hidden" name="id" value="{{ product.variants.first.id }}"> 
```
where  current_variant = product.selected_or_first_available_variant
```
<input type="hidden" name="id" value="{{ current_variant.id }}">
```

This theme starter also included a few css rules:
 
 One to hide the preview bar as it always seems to be in the way while designing.
 
 One to hide the extra input selectors that are generated when js is turned off.  The code iterates through the product variants in the ```<no script>``` tag and also through product.options in the liquid that is intended to work by js selecting a variant  based on product.options in real time, while also managing the history state and visible url to reflect the current variant. Thus without hiding the selectors two sets will show when js is turned off.

More Issues:

PRICES:

 There are some more issues when prices and other things change the code uses document.innerText

  1. {{ current_variant.compare_at_price | money }} returns a span
  2.  document.innerHtml outputs everything as text so it prints out the html tags to the screen
  3. this is probably not what is wanted.
  4. insertAdjacentHTML works well along with document.innerText to remove what was there before but it could be a xss risk though this is not user generated content and is coming from the server without modification.
  5. could write a function that inserts only the price inside the span instead of the whole span.
  6. works without javascript enabled becuse liquid just inserts the span into the document but obviously does not update on selection so its useless.
