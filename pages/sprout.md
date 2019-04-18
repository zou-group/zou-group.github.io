---
layout: page
title: "Sprout"
permalink: "/SPROUT"
header:
    image_fullwidth: "header_unsplash_1.jpg"
---

<head>
    <meta charset="UTF-8">
    <title>SPROUT</title>
    <link href="stylesheets/flatui/dist/css/vendor/bootstrap.min.css" rel="stylesheet">
    <link href="stylesheets/flatui/dist/css/flat-ui.min.css" rel="stylesheet">
</head>
<body>
<div style="margin: 50px;">
	<h4>SPROUT</h4>
	<a href="https://www.biorxiv.org/content/10.1101/404947v1">SPROUT is a machine learning algorithm that predicts the DNA repair outcome in CRISPR-CAS9 experiments.</a>
	<br>
	<a href="https://github.com/amirmohan/SPROUT">Click to access the source code.</a>
	<br>
	<br>
	Input the 20-nucleotide sgRNA sequence followed by the 3-nucleotide PAM sequence
	<br>
	(e.g., CCACCAAAGTACGATGTGAGAGG)
	<br>
	<br>
		<div>
			<form method="POST" action="http://13.59.18.211:80">

				<textarea type="text" name="inp" placeholder="Input sequence ..." class="form-control" style="width: 880px; height: 100px;"></textarea>
				<br>
				<p>Input: </p>
				<div class="share" style="width: 400px;">
					
				</div>
				<br>
				<p>Output: </p>
				<div class="share" style="width: 400px;">
					
					
					
				</div>
				<br>
				<input type="submit" value="Predict" class="btn btn-primary" style="width: 150px;" />
			</form>
		</div>
	</div>
</div>

</body>
</html>
