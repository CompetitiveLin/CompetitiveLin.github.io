# **Powered by [Jekyll](https://jekyllrb.com/) & [Minimal Mistakes](https://github.com/mmistakes/minimal-mistakes).**

# Snippet in VS code
``` json
{
	// Place your global snippets here. Each snippet is defined under a snippet name and has a scope, prefix, body and 
	// description. Add comma separated ids of the languages where the snippet is applicable in the scope field. If scope 
	// is left empty or omitted, the snippet gets applied to all languages. The prefix is what is 
	// used to trigger the snippet and the body will be expanded and inserted. Possible variables are: 
	// $1, $2 for tab stops, $0 for the final cursor position, and ${1:label}, ${2:another} for placeholders. 
	// Placeholders with the same ids are connected.
	// Example:
	"Print to console": {
		"prefix": "md",
		"body": [
			"---",
			"title: &title $1",
			"excerpt: $2",
			"categories: [\"$3\"]",
		  	"tags: $4",
			"date: ${CURRENT_YEAR}-${CURRENT_MONTH}-${CURRENT_DATE}T${CURRENT_HOUR}:${CURRENT_MINUTE}:${CURRENT_SECOND}+800",
			"header:",
			"  overlay_image: /assets/images/unsplash-image-1.jpg",
		  	"show_date: true",
		  	"toc: true",
			"toc_label: *title",
		  	"toc_sticky: true",
		 	"last_modified_at:",
			 "---",
			 "$0"
		],
		"description": "Markdown template for jekyll."
	}
}
```