---
layout: default
---

Temporary link back to old version: [Old Homepage](https://www.dsouza.uk/BoardGameHelper2/)

# Games List

<div id="GamesList">
</div>




<script>
var MinDivOuterWidth = 1;




function GamesListWidthResize() {
	var SectionWidth = $("#GamesList").width();
	var DivWidth;

	DivWidth = SectionWidth / parseInt(SectionWidth / MinDivOuterWidth);
	$("#GamesList div").outerWidth(DivWidth);
}





$(document).ready(function(){
	var BGGIDList = "";
	var html = "";

	$.get( //Get Games List from internal XML
		"{{ 'Games/GamesList.xml' | relative_url }}?v={{ site.github.build_revision }}"
		,function(data){
			BGGIDList = $(data).find("Games Game").map(function(){
				return $(this).attr("bggid");
			})
			.get()
			.join();
		}
	)
	.done(function(){
		$.get( //Get BGGAPI information
			"{{ site.bggapi-thing }}" + BGGIDList
			,function(data){
				//Item List
				var item = $(data).find("items item");

				//Sort Names of Games Alphabetically
				item.sort(function(a,b){
					return ($(a).find("name[type='primary']").attr("value") > $(b).find("name[type='primary']").attr("value")) ? 1 : 0;
				});

				//Create HTML
				item.each(function(i,v){
					html +=	"<div>"
						+		"<a href='Games/?bggid=" + $(v).attr("id") + "'>"
						+		"<span class='thumbnail'><img src='" + $(v).find("thumbnail").text() + "'></span>"
						+		"<span>" + $(v).find("name[type='primary']").attr("value") + "</span>"
						+		"</a>"
						+	"</div>";
				});
			}
		)
		.done(function(){
			//Insert HTML into DOM
			$("#GamesList").html(html);

			//Resize Items
			MinDivOuterWidth = $("#GamesList div").outerWidth(true);
			GamesListWidthResize();

			//Bind Windows Resize
			$(window).resize(function(){
				GamesListWidthResize();
			});
		});
	});
});
</script>
