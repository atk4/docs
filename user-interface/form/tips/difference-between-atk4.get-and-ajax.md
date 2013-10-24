### Difference between $.atk4.get() and $.ajax

It is worth noting that instead of using `$.ajax`, form uses `$.atk4.get()`, which introduces number of improvements (timeout checking, on-js-loaded handler, tracking number of files being loaded, support for object-based URLs) but also relies on `$.ajax`. For more information on AJAX functions, see AJAX documentation of Agile Toolkit