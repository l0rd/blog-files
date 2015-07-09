This are usefull regexp to find replace markdown syntax to dotclear syntax. I've just used it within sublmie text find and replace panel for now.

__TODO__ : Script it

## Titles 
Replace all
	###
With
    !

Replace all
	##
With
    !!

Replace all
	#
With
    !!!

## Images
Replace all 
	\!\[\(\w+)]\((\w+)\.png\)
With
	((/public/Billet_0xxx/\2.png|\1|C))

## Urls
Replace all
	\[(.*?)\]\((.*?)\)
With
	[\1|\2]

## Emphasis
Replace all
	\*(.*?)\*
With
    ''\1''

## Strong Emphasis
Replace all
	\*\*(.*?)\*\*
With
    __\1__


## Inline Code (regexp does not work)
Replace all
	`(.*?)`
With
	@@\1@@


## Blog Code (regexp does not work)
Replace all
	```
	(.*?)
	```
With
	///[java]
    \1
	///

