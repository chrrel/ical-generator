ical-generator
==============

This script creates and updates an ical-file (.ics) by using data from a Custom Post Type of your WordPress site.

What you have to customize - 7 steps
------------------------------------

The Lines marked with "!" have to be changed to make the script work.


Line  
42   Modify the Category 

61   Modify the UID

72 ! Change the path to your Wordpress files

75 ! Replace the post type with your post type's name

79   If wanted modify maxdate. Only events that are yet to come or took place after maxdate are published in the ical-file.

87-93 ! Change the meta key names to yours.

131 Change the parameters of write_feed() 


