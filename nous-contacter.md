---
layout: french
title: Retrouvez Les différents moyens pour contacter Jaxio
description: Contactez nous via mail, téléphone et formulaire 
---

# Nous contacter
<div class="span-24 last">
<h2>Directement</h2>
</div>


<div id="email" class="span-6">
<p>
Téléphone: +33 (0)6 18 65 03 89
<br/>
Email: <a href="mailto:info@jaxio.com">info@jaxio.com</a>
</p>
<br/>
<br/>
</div>

<div id="address" class="span-18 last">
<p>JAXIO SARL
<br/>
51, rue Le Peletier
<br/>
75009 Paris (<a href="http://maps.google.fr/maps?f=q&source=s_q&hl=fr&geocode=&q=51+Rue+Le+Peletier,+Paris&sll=46.75984,1.738281&sspn=11.215826,28.54248&ie=UTF8&hq=&hnear=51+Rue+Le+Peletier,+75009+Paris,+Ile-de-France&ll=48.875868,2.339857&spn=0.002628,0.006968&t=h&z=18">Carte</a>)
</p>
<br/>
<br/>
</div>

<div class="span-24 last">
<h2>Via ce formulaire</h2>
</div>
<div id="questionnaire" class="span-24 last">
	<style>
		form#contact_form label {
			display: inline-block;
			width:100px;
			padding: 0px;
			text-align: right;
		}
		form#contact_form label.required {
			font-weight: bold;
		}
	</style>
       	<form action="https://spreadsheets.google.com/spreadsheet/formResponse?formkey=dFBRSzJDeWZaZWJyV2ozS3FEX3Zld2c6MQ&amp;ifq&amp;theme=0AX42CRMsmRFbUy03NTAzM2Q4My03ODU1LTQ2NzItODI2YS1kZmU5YzdiMzZjOGQ" 
			method="POST" id="contact_form" target="google_form" onsubmit="submitted=true;">
		<input type="hidden" name="pageNumber" value="0"/>
		<input type="hidden" name="backupCache"/>
		<fieldset>
			<p>
				<label for="firstname" class="required">Prénom</label>
				<input id="firstname" type="text" name="entry.0.single" placeholder="Requis" required/>
			</p>
			<p>
				<label for="lastname" class="required">Nom</label>
				<input id="lastname" type="text" name="entry.2.single" placeholder="Requis" required/>
			</p>
			<p>
				<label for="email" class="required">Email</label>
				<input id="email" type="email" name="entry.4.single" placeholder="Requis" required/>
			</p>
			<p>
				<label for="title">Titre</label>
				<input id="title" type="text" name="entry.9.single" placeholder="Optionnel"/>
				<label for="company">Société</label>
				<input id="company" type="text" name="entry.11.single" placeholder="Optionnel"/>
				<label for="phone">Téléphone</label>
				<input id="phone" type="phone" name="entry.6.single" placeholder="Optionnel"/>
			</p>
			<p>
				<label for="message" class="required" style="vertical-align:top;">Message</label>
				<textarea id="message" name="entry.8.single" placeholder="Votre message..." style="width:570px;height: 150px;" required></textarea>
			</p>
			<p>
				<label for="sendit" style="vertical-align:top;">&nbsp;</label>			
				<input id="sendit" type="submit" name="submit" value="Envoyer" style="padding:5px;font-size:15px;width:200px;border: 1px solid #BEBEBE;background-color: white;"/><br>
			</p>
		</fieldset>
	</form>
</div>

<div id="confirmation" style="display: none" class="notice">
Nous avons reçu votre message, nous y répondrons dans les meilleurs délais.
</div>

<!-- see http://www.morningcopy.com.au/tutorials/how-to-style-google-forms/ -->
<script type="text/javascript">
	var submitted=false;
	function formLoaded() {
		if(submitted) {
			$("#questionnaire").hide();
			$("#confirmation").show();
		} 
	}
</script>
<iframe name="google_form"
	style="display:none;"
	src="https://spreadsheets.google.com/embeddedform?formkey=dDVxTzhZY0tfaXNoYWVCV0RvWHJWd1E6MQ" 
	onload="formLoaded();">
		Loading...
</iframe>
