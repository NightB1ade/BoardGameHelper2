---
---

Temporary link back to old version: [Old Homepage](https://www.dsouza.uk/BoardGameHelper2/)

# Games List

<div id="GamesList">
</div>

# JSON Editor

[Games
List](https://json-editor.github.io/json-editor/?data=N4Ig9gDgLglmB2BnEAuUMDGCA2MBGqIAZglAIYDuApomALZUCsIANOHgFZUZQD62ZAJ5gArlELwwAJzplsrEIgwALKrNShYUbFUIAFKlNrwFUQRF0p2XHgqlUAjiJj2AJqgDaIAOZkGyAF02CClIQ1gaDR8/SLQQMwtCMikpIQUSGTJxK3I8HVMYbUsQAHEY5DYReBgnKgBJKDVkFCgpESo2QqaohOKwTm5xNi18qzKGO0dnN08QPG9vGHc2eBiQIJAQsKkI5tB5xfc43sIYeEbvQwKiwgAhEpKAAjqAEQU6M5g6ETpUAEYAL4rNbHczFRCtM7ea6jEAAOTWbA+8AAMlR4N4oMpUAAWAH4glsRDKMAUXiGUJGU7nQxkHhwEzDVQTKx4MBgKAQ1IQHEgfFAA=){:target="_blank"}, [JSON Schema](JsonSchemas/GamesList.schema.json)




<script>
	var MinDivOuterWidth = 1;




	function GamesListWidthResize() {
		var SectionWidth = $("#GamesList").width();
		var DivWidth;

		DivWidth = SectionWidth / parseInt(SectionWidth / MinDivOuterWidth);
		$("#GamesList div").outerWidth(DivWidth);
	}




	$(document).ready(function() {
		var BGGIDList = "";
		var html = "";

		$.get( //Get Games List from internal JSON
				"GamesList.json?v={{ site.github.build_revision }}"
				, function(data) {
					data.games.forEach(function(value, index, array) {
						BGGIDList = BGGIDList + value.bggid + ",";
					});
					BGGIDList = BGGIDList.substr(0, BGGIDList.length - 1);
				})
			.done(function() {
				$.get( //Get BGGAPI information
						"{{ site.bggapi-thing }}" + BGGIDList
						, function(data) {
							//Item List
							var item = $(data).find("items item");

							//Sort Names of Games Alphabetically
							item.sort(function(a, b) {
								return (
										$(a).find("name[type='primary']").attr("value") >
										$(b).find("name[type='primary']").attr("value")
									) ?
									1 :
									0;
							});

							//Create HTML
							item.each(function(i, v) {
								html += "<div>" +
									"<a href='Games/?bggid=" + $(v).attr("id") + "'>" +
									"<span class='thumbnail'><img src='" + $(v).find("thumbnail").text() + "'></span>" +
									"<span>" + $(v).find("name[type='primary']").attr("value") + "</span>" +
									"</a>" +
									"</div>";
							});
						}
					)
					.done(function() {
						//Insert HTML into DOM
						$("#GamesList").html(html);

						//Resize Items
						MinDivOuterWidth = $("#GamesList div").outerWidth(true);
						GamesListWidthResize();

						//Bind Windows Resize
						$(window).resize(function() {
							GamesListWidthResize();
						});
					});
			});
	});
</script>
