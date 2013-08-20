# Assets URL Routing

Since your Agile Toolkit application can be located in needs a little bit of magic to be able to access it's asset. Internally - assets would be located in one of the defined locations.

I will just remind you that you typically refer to asset locations by surrounding them with several special template tags:

    <?public?>image/logo.png<?/?>

In Agile Toolkit all locations can have a specific `base_url` associated with them (See documentation on PathFinder for more information). When your application refers to an image, Agile Toolkit first tries to locate it throughout available locations in appropriate order.

When the asset is located, the `base_path` of that location is prepended to the relative location of the image.

If you wish to introduce custom logic in asset positioning you could customize your pathfinder location to have a different `base_path`



