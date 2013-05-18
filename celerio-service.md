---
layout: french
title: Service Celerio - Evaluation de Celerio
description: Générez votre projet en ligne avec le service Celerio. 
---

## Service Celerio

Le service Celerio (anciennement SpringFuse) vous permet de générer un projet sur notre serveur et de le télécharger.

Ainsi, vous pouvez évaluer le code source généré par Celerio et le **gain de productivité** qu'il apporte.

<div><g:plusone></g:plusone></div>

__Avec la version en ligne de Celerio la génération se déroule de cette manière__:

* Vous remplissez le formulaire ci-dessous pour **préparer** le lancement sur votre machine de notre plugin maven.
* Notre plugin maven réalise une **rétro-ingénierie** de votre base de données pour récupérer
le nom des tables, le nom des colonnes, les contraintes d'intégrités, etc. En aucun cas, cette rétro-ingénierie ne récupère des vraies données.
Seules les métadonnéées sont extraites. 
 * Notre plugin **uploade** ces métadonnées sur notre serveur ainsi que le fichier de configuration
`maven-celerio-plugin.xml` présent dans le répertoire `src/main/config`. Ce fichier permet de configurer la génération de code.
* Une fois l'upload terminé, <a href="/celerio.html" target="_new">Celerio</a> est appelé sur notre serveur pour **générer**
et zipper votre projet.
* Notre plugin **télécharge** ensuite votre projet et le dézippe.
Et voilà vous avez votre première release!


__Pre requis__

* Au moins Java 1.6 (build récent)
* Au moins Maven 2

`Pour générer un projet`, remplissez le formulaire ci-dessous 
puis exécutez dans une console les commandes maven résultantes.

### Etape 1/4: Détails du projet 

<form class="form-horizontal">

<div class="alert alert-info">
	Informations sur le projet
</div>

<div class="control-group celerioVersion">
    <label class="control-label" for="celerioVersion">Version de Celerio</label>
    <div class="controls">
		<select id="celerioVersion" name="celerioVersion" class="updateCommand">
			<option value="3.0.100">3.0.100</option>
			<option value="3.0.101-SNAPSHOT">3.0.101-SNAPSHOT</option>
		</select>    
	</div>
</div>

<!-- artifactId -->
<div class="control-group artifactId">
    <label class="control-label" for="artifactId">Nom du projet</label>
    <div class="controls">
		<input type="text" id="artifactId" value="monprojet" placeholder="ex: monprojet" class="required lettersNumbers updateCommand"/>
		<span class="help-inline">Seuls les caractères simples sont permis, a-z, A-Z, 0-9, ex: monprojet</span>
	</div>
</div>

<!-- groupId -->
<div class="control-group groupId">
    <label class="control-label" for="groupId">Package Java</label>
    <div class="controls">
		<input type="text" id="groupId" size="40" value="fr.masociete.monprojet" placeholder="ex: fr.jaxio.demo" class="required updateCommand" title="Ex: fr.masociete.monprojet"/>
    </div>
</div>

<!-- email -->
<div class="control-group email">
    <label class="control-label" for="email">Email</label>
    <div class="controls">
		<input type="text" id="email" class="required updateCommand"/>
		<span class="help-inline">Le mail devra être confirmé lors de la 1ère génération</span>
    </div>
</div>

<hr/>
<div class="control-group">
	<label class="control-label" for="frontEnd">Type de Project</label>
    <div class="controls">
      <label class="radio">
		<input type="radio" name="frontEnd" id="jsf2Spring" value="jsf2Spring" class="updateCommand" checked="checked"/>JSF 2, Primefaces 3.5, avec une API conversation
			<span class="help-block">
				Parfait pour une application d'entreprise orientée données. Le code généré est 
				bien organisé, propose des fonctionnalités puissantes, contient une API inégalée de `conversation`, etc.
				C'est notre option préférée. Avec vous allez booster votre productivité.
			</span>
	  </label>	  
      <label class="radio">
		<input type="radio" name="frontEnd" id="jsf2Simple" value="jsf2Simple" class="updateCommand" />JSF 2, Primefaces 3.5
			<span class="help-block">
				Pareil que l'option ci-dessus, mais sans les conversations... plus simple mais beaucoup moins puissante.
			</span>
	  </label>	  
      <label class="radio">
      <input type="radio" name="frontEnd" id="backendJpa" value="backendJpa" class="updateCommand"/>JPA 2 backend, Query By Example feature
		<span class="help-block">      
      		Juste la partie backend... Parfait si vous ne voulez pas du front JSF...
		</span>
      </label>
    </div>
</div>

<div class="alert alert-info">
	Rétro-ingénierie de la base de données
</div>

<!-- use example or reverse? -->
<div class="control-group">
	<label class="control-label" for="archetypeArtifactId3">Au choix</label>
    <div class="controls">
      <label class="radio">
			<input type="radio" name="archetypeArtifactId" id="archetypeArtifactId3" value="quickstart-embedded-db-with-configuration" class="updateCommand" checked="checked"/>Utiliser une base de données d'exemple, fournie par Jaxio.
	  </label>
      <label class="radio">
			<input type="radio" name="archetypeArtifactId" id="archetypeArtifactId1" value="quickstart" class="updateCommand"/>Utiliser votre propre base de données.
      </label>
    </div>
</div>


<!-- dbType -->
<div class="jdbc-properties" style="display: none">
<p>
	<b>Base à rétro-ingénierer:</b>
</p>
<div class="control-group">
	<label class="control-label" for="dbType">Vendeur</label>	
	<div class="controls">
		<select id="dbType" name="dbType" class="updateCommand">
			<option value="mysql">mysql</option>
			<option value="oracle">oracle</option>
			<option value="db2">db2</option>
			<option value="h2">h2</option>
			<option value="postgresql">postgresql</option>
			<option value="other">autre</option>
		</select>
	</div>
</div>

<p>
	<b>Si besoin, remplacez les valeurs ci-dessous:</b>
</p>

<div id="oracle-database" class="alert alert-warn" style="display: none">
	Si vous n'avez pas de driver JDBC Oracle déjà installé dans votre repository maven, vous devez  en
	<a href="/documentation/celerio/miscellaneous.html#install-oracle-jdbc-driver-maven-repository" target="_new">installer un à la main</a>.
	<br/>
	Si vous utilisez des séquences pour générer la valeur de vos clés primaires, vous devez configurer Celerio 
	à l'étape 3/4 plus bas. Reportez-vous à la documentation <a href="/documentation/celerio/configuration.html#seq_per_table" target="_new">'Use a SEQUENCE per TABLE'</a> 
</div>
<div id="db2-database" class="alert alert-warn" style="display: none">
	Si vous n'avez pas de driver JDBC DB2 déjà installé dans votre repository maven, vous devez en
	<a href="/documentation/celerio/miscellaneous.html#install-db2-jdbc-driver-maven-repository" target="_new">installer un à la main</a>.
</div>
<div id="other-database" class="alert alert-warn" style="display: none">
	Merci de renseigner les valeurs ci-dessous avec les valeurs correspondant à votre driver. 
</div>


<!-- jdbcGroupId -->
<div class="control-group">
	<label class="control-label" for="jdbcGroupId">groupId Maven</label>
    <div class="controls">
		<input type="text" name="jdbcGroupId" id="jdbcGroupId" class="updateCommand" placeholder="ex: mysql">
    </div>
</div>
	
<!-- jdbcArtifactId -->
<div class="control-group">
	<label class="control-label" for="jdbcArtifactId">artifactId Maven</label>
    <div class="controls">	
		<input type="text" name="jdbcArtifactId" id="jdbcArtifactId" class="updateCommand" placeholder="ex: mysql-connector-java"/>
	</div>		
</div>

<!-- jdbcGroupId -->
<div class="control-group">
	<label class="control-label" for="jdbcVersion">Version</label>
    <div class="controls">	
		<input type="text" name="jdbcVersion" id="jdbcVersion" class="updateCommand" placeholder="ex: 5.1.17"/>
	</div>
</div>	

<!-- jdbcDriver -->
<div class="control-group">
	<label class="control-label" for="jdbcDriver">Classe Java du Driver</label>
    <div class="controls">
		<input type="text" name="jdbcDriver" id="jdbcDriver" class="updateCommand" placeholder="ex: com.mysql.jdbc.Driver"/>
	</div>
</div>

<p>
	<b>Maintenant saisissez les informations pour se connecter à votre base</b>. N'utilisez pas une base de données de production!
</p>

<!-- jdbcUrl -->
<div class="control-group">
	<label class="control-label" for="jdbcUrl">Url Jdbc</label>
    <div class="controls">	
		<input type="text" name="jdbcUrl" id="jdbcUrl" class="updateCommand" placeholder="ex: jdbc:mysql://localhost/DBNAME"/>
	</div>
</div>

<!-- jdbcUser -->
<div class="control-group">
	<label class="control-label" for="jdbcUser">Utilisateur BdD</label>
    <div class="controls">
		<input type="text" name="jdbcUser" id="jdbcUser" class="updateCommand" placeholder="ex: user"/>
	</div>		
</div>

<!-- jdbcPassword -->
<div class="control-group">
	<label class="control-label" for="jdbcPassword">Mot de passe</label>
    <div class="controls">
		<input type="text" name="jdbcPassword" id="jdbcPassword" class="updateCommand" placeholder="ex: password"/>
	</div>		
</div>
</div>

<div class="alert alert-info">
	<i class="icon-wrench"> </i> Réglages Proxy 
</div>

<!-- proxyEnable ? -->
<div class="control-group">
	<label class="control-label" for="proxyEnable">Etes-vous derrière un proxy ?</label>
    <div class="controls">
      <label class="radio">
		<input type="radio" name="proxyEnable" id="proxyEnableFalse" value="false" class="updateCommand" checked="checked"/>Non
	  </label>
      <label class="radio">
		<input type="radio" name="proxyEnable" id="proxyEnableTrue" value="true" class="updateCommand"/>Oui
      </label>
    </div>
</div>

<div class="proxy-properties" style="display: none">
	<p><b>Ok, il faut les informations suivantes:</b></p>

	<div class="control-group">
		<label class="control-label" for="proxyHost">Adresse du proxy</label>
		<div class="controls">	
			<input type="text" name="proxyHost" id="proxyHost" class="updateCommand" value="intranet-proxy"/>
		</div>	
	</div>
	
	<div class="control-group">
		<label class="control-label" for="proxyPort">Port du proxy</label>
		<div class="controls">	
			<input type="text" name="proxyPort" id="proxyPort" class="updateCommand" value="8080"/>
		</div>	
	</div>
	
	<div class="control-group">
		<label class="control-label" for="proxyUsername">Login</label>
		<div class="controls">	
			<input type="text" name="proxyUsername" id="proxyUsername" class="updateCommand" placeholder="ex: user"/>
		</div>	
	</div>
	
	<div class="control-group">
		<label class="control-label" for="proxyPassword">Mot de passe</label>
		<div class="controls">	
			<input type="password" name="proxyPassword" id="proxyPassword" class="updateCommand" placeholder="ex: password"/>
		</div>	
	</div>
	
	<div class="control-group">
		<label class="control-label" for="proxyNtlmDomain">Domain</label>
		<div class="controls">	
			<input type="text" name="proxyNtlmDomain" id="proxyNtlmDomain" class="updateCommand" placeholder="ex: mydomain"/>
			<span class="help-block">only if your proxy uses ntlm</span>
		</div>	
	</div>

	<div class="control-group">
		<label class="control-label" for="proxyNtlmWorkstation">Workstation</label>
		<div class="controls">	
			<input type="text" name="proxyNtlmWorkstation" id="proxyNtlmWorkstation" class="updateCommand" placeholder="ex: mymachine"/>
			<span class="help-block">Seulement si votre proxy utilise NTLM.
			<br/>
			Sous windows, pour trouver le nom de votre ordinateur (i.e. Workstation)
				<ul>
					<li>ouvrir Système en cliquant sur le bouton Démarrage,</li>
					<li>ouvrir Control Panel,</li>
					<li>ouvrir System and Maintenance,</li>
					<li>cliquer sur System.</li>
				</ul>							
			</span>
		</div>
	</div>
	
	<div class="alert alert-warn">
		Si ce n'est déjà fait, vous devez aussi <a href="http://maven.apache.org/guides/mini/guide-proxies.html" target="_new">configurer maven pour qu'il utilise votre proxy</a>.
	</div>	
</div>
</form>
<hr/>

Une fois que vous avez <span class="label label-info">renseigné les informations demandées</span>, 
copier-coller séquentiellement les commandes maven des étapes 2 et 4 dans une console:

### Etape 2/4: Téléchargement de l'archetype maven

<p><span class="label label-info">Copier-coller</span> la commande maven suivante pour récupérer les quelques fichiers nécessaires à la rétro-ingénierie de la base de données (i.e. un pom.xml, etc.).</p>

<textarea id="cmdLine" rows="6" cols="80" style="width:850px;height:80px;"></textarea>

### Etape 3/4:  Configuration (Optionelle)

Après avoir fait <span class="label label-info">vos premières armes</span> avec ce service, vous allez surement vouloir <a href="http://www.jaxio.com/documentation/celerio/configuration.html" target="_new">configurer la génération de code</a>.

### Etape 4/4: Rétro-ingénierie et Génération de code

<span class="label label-info">Copier-coller</span> ces commandes dans une console pour: 

* faire la rétro-ingénierie de votre base de données, ou de celle fournie par Jaxio
* envoyer les metadonnées (pas les données) de la base de données à notre serveur 
* télécharger le projet généré depuis notre serveur et le dézipper
* déployer votre projet sur votre machine en utilisant le conteneur de servlet Jetty (<a href="http://localhost:8080/">http://localhost:8080/</a>) ou, s'il s'agit d'un projet backend, lancer les tests unitaires.

<textarea id="cmdLine2" rows="2" cols="80" style="width:850px;height:40px;"></textarea>


<p class="tip"> 
	A la première utilisation ce service, soyez patient, Maven doit télécharger beaucoup de dépendences...  
</p>

<script type="text/javascript">
	function updateDbTypeDefaultValues() {
		var urlDbType = $("#jdbcUrl").val().split(":")[1];
		var dbType = $("#dbType").val();
		if (dbType == "h2") {
			if (urlDbType != "h2") {
				$("#jdbcUrl").val("jdbc:h2:~/.h2/DBNAME");
			}
			$("#jdbcGroupId").val("com.h2database");
			$("#jdbcArtifactId").val("h2");
			$("#jdbcDriver").val("org.h2.Driver");
			$("#jdbcVersion").val("1.3.165");
		} else if (dbType == "postgresql") {
			if (urlDbType != "postgresql") {
				$("#jdbcUrl").val("jdbc:postgresql://localhost/DBNAME");
			}
			$("#jdbcGroupId").val("postgresql");
			$("#jdbcArtifactId").val("postgresql");
			$("#jdbcDriver").val("org.postgresql.Driver");
			$("#jdbcVersion").val("8.2-504.jdbc3");
		} else if (dbType == "oracle") {
			if (urlDbType != "oracle") {
				$("#jdbcUrl").val("jdbc:oracle:thin:@localhost:1521:XE");
			}
			$("#jdbcGroupId").val("com.oracle");
			$("#jdbcArtifactId").val("ojdbc14");
			$("#jdbcDriver").val("oracle.jdbc.driver.OracleDriver");
			$("#jdbcVersion").val("10.2.0.3");
		} else if (dbType == "db2") {
			if (urlDbType != "db2") {
				$("#jdbcUrl").val("jdbc:db2://localhost:50000/DBNAME");
			}
			$("#jdbcGroupId").val("com.ibm.db2");
			$("#jdbcArtifactId").val("db2jcc4");
			$("#jdbcDriver").val("com.ibm.db2.jcc.DB2Driver");
			$("#jdbcVersion").val("9.7.0.4");
		} else if (dbType == "mysql") {
			if (urlDbType != "mysql") {
				$("#jdbcUrl").val("jdbc:mysql://localhost/DBNAME");
			}
			$("#jdbcGroupId").val("mysql");
			$("#jdbcArtifactId").val("mysql-connector-java");
			$("#jdbcDriver").val("com.mysql.jdbc.Driver");
			$("#jdbcVersion").val("5.1.17");
		}

		if (dbType == "oracle") {
			$("#oracle-database").show();
		} else {
			$("#oracle-database").hide();
		}
		if (dbType == "db2") {
			$("#db2-database").show();
		} else {
			$("#db2-database").hide();
		}
		
		if (dbType == "other") {
			$("#other-database").show();
		} else {
			$("#other-database").hide();
		}
	}

	function param(key, value) {
		if (!value) {
			value = "";
		}
		return "-D" + key + "=" + value.replace(/ /gi, "\\ ") + " ";
	}
	function isValidEmailAddress(emailAddress) {
		var pattern = new RegExp(/^(("[\w-\s]+")|([\w-]+(?:\.[\w-]+)*)|("[\w-\s]+")([\w-]+(?:\.[\w-]+)*))(@((?:[\w-]+\.)*\w[\w-]{0,66})\.([a-z]{2,6}(?:\.[a-z]{2})?)$)|(@\[?((25[0-5]\.|2[0-4][0-9]\.|1[0-9]{2}\.|[0-9]{1,2}\.))((25[0-5]|2[0-4][0-9]|1[0-9]{2}|[0-9]{1,2})\.){2}(25[0-5]|2[0-4][0-9]|1[0-9]{2}|[0-9]{1,2})\]?$)/i);
		return pattern.test(emailAddress);
	}

	function updateCommand() {
		var version= $("#celerioVersion").val();
		var groupId = $("#groupId").val();
		var artifactId = $("#artifactId").val();
		var email= $("#email").val();
		var springData = $("input[name=springData]:checked").val();
		var frontEnd = $("input[name=frontEnd]:checked").val();
		var archetypeArtifactId = $("input[name=archetypeArtifactId]:checked").val();
		var proxyEnable = $("input[name=proxyEnable]:checked").val();

		if (springData) {
			frontEnd = frontEnd + "Sd";
		}

		$(".groupId").toggleClass("error", !groupId.match(/^([a-zA-Z]+){1}(\.[a-zA-Z0-9_]+)*$/));
		$(".artifactId").toggleClass("error", !artifactId.match(/^[a-zA-Z0-9]+$/));
		$(".email").toggleClass("error", email.length > 0 && !isValidEmailAddress(email));

		if (archetypeArtifactId == "quickstart") {
			$(".jdbc-properties").show();
		} else {
			$(".jdbc-properties").hide();
		}

		if (proxyEnable === "true") {
			$(".proxy-properties").show();
		} else {
			$(".proxy-properties").hide();
		}

		var cmd = 'mvn -U archetype:generate ';
		var cmd2 = '';
		cmd += param("archetypeGroupId", "com.springfuse.archetypes");
		cmd += param("archetypeArtifactId", archetypeArtifactId);
		cmd += param("archetypeVersion", version);
		cmd += param("groupId", groupId);
		cmd += param("package", groupId);
		cmd += param("artifactId", artifactId);
		cmd += param("version", "1.0.0");
		cmd += param("frontEnd", frontEnd);
		cmd += param("email", email);
		cmd += param("password", "none");

		if (archetypeArtifactId == "quickstart") {
			$("#cmdLine").val("");
			var jdbcGroupId = $("#jdbcGroupId").val();
			var jdbcArtifactId = $("#jdbcArtifactId").val();
			var jdbcVersion = $("#jdbcVersion").val();
			var jdbcUrl = $("#jdbcUrl").val();
			var jdbcDriver = $("#jdbcDriver").val();
			var jdbcUser = $("#jdbcUser").val();
			var jdbcPassword = $("#jdbcPassword").val();

			$("#jdbcGroupId").toggleClass("error", !jdbcGroupId.match(/^\w+(\.\w+)*$/));
			$("#jdbcArtifactId").toggleClass("error", !jdbcArtifactId.match(/^[\w\.\_\-]+$/));
			$("#jdbcVersion").toggleClass("error", !jdbcVersion.match(/^[\w\.\_\-]+$/));
			$("#jdbcUrl").toggleClass("error", !jdbcUrl.match(/^jdbc:.*/));
			$("#jdbcDriver").toggleClass("error", !jdbcDriver.match(/^[\w\.\_\-]+$/));

			cmd += param("jdbcGroupId", jdbcGroupId);
			cmd += param("jdbcArtifactId", jdbcArtifactId);
			cmd += param("jdbcVersion", jdbcVersion);
			cmd += param("jdbcDriver", jdbcDriver);
			cmd += param("jdbcUser", jdbcUser);
			cmd += param("jdbcPassword", jdbcPassword);
			cmd += param("jdbcUrl", jdbcUrl);
			$("#cmdLine").attr("rows", "12");
		} else {
			$("#cmdLine").attr("rows", "10");
		}
		cmd += param("interactiveMode", "false");
		// proxy
		if (proxyEnable === "true") {
			var proxyHost = $("#proxyHost").val();
			var proxyPort = $("#proxyPort").val();
			var proxyUsername = $("#proxyUsername").val();
			var proxyPassword = $("#proxyPassword").val();
			var proxyNtlmDomain = $("#proxyNtlmDomain").val();
			var proxyNtlmWorkstation = $("#proxyNtlmWorkstation").val();

			$("#proxyHost").toggleClass("error", !proxyHost.match(/^[\w\.\_\-]+$/));
			$("#proxyPort").toggleClass("error", !proxyPort.match(/^\d+$/));

			cmd += param("proxyEnable", "true")
			cmd += param("proxyHost", proxyHost)
			cmd += param("proxyPort", proxyPort)
			if (proxyUsername) {
				cmd += param("proxyUsername", proxyUsername)
				cmd += param("proxyPassword", proxyPassword)
			}
			if (proxyNtlmDomain) {
				cmd += param("proxyNtlmEnable", "true")
				cmd += param("proxyNtlmDomain", proxyNtlmDomain)
				cmd += param("proxyNtlmWorkstation", proxyNtlmWorkstation)
			}
		}
		if(window.location.host.indexOf('localhost') != 0){
			cmd += param("archetypeRepository", "http://maven2.springfuse.com/")
		}
		cmd += "\n";
		cmd += 'cd ' + artifactId + '\n';
		if(window.location.host.indexOf('localhost') == 0){
			cmd2 = 'mvn -f springfuse.xml generate-sources -Dmaven-remote-generation-plugin.generationServiceLocation=http://localhost:9999/remote/generate\n';
		} else {
			cmd2 = 'mvn -f springfuse.xml generate-sources\n';
		}

		if (frontEnd !== "backendJpa") {
			$(".open-your-browser").show();
		} else {
			$(".open-your-browser").hide();
		}
		$("#cmdLine").val(cmd);
		$("#cmdLine2").val(cmd2);
		$(".project-name").html(artifactId);

		$("#destinationUrl").html("<a href=\"http://localhost:8080/" + artifactId + "\">http://localhost:8080/" + artifactId + "</a>");

		if($(".error:visible").length > 0) {
			$("#cmdLine").css({"background-color": "red","color": "white"});
		} else {
			$("#cmdLine").css({"background-color": "white", "color": "black"});
		}
	}

	$(document).ready(function() {
		$(".updateCommand").change(updateCommand);
		$("#cmdLine").click(function() {
			$(this).select();
		});
		$("#cmdLine2").click(function() {
			$(this).select();
		});
		$("#jdbcUrl").change(function() {
			var dbType = $("#jdbcUrl").val().split(":")[1];
			if ($("#dbType option:contains('" + dbType + "')").val()) {
				$("#dbType").val(dbType);
			} else {
				$("#dbType").val("other")
			}
			updateDbTypeDefaultValues();
			updateCommand();
		});
		$("#dbType").change(function() {
			updateDbTypeDefaultValues();
			updateCommand();
		});
		$("#version").change(function() {
			updateCommand();
		});
		$("#frontEnd").change(function() {
			updateCommand();
		});
		
		if(window.location.href.indexOf('archetypeArtifactId=quickstart-embedded-db-with-configuration') > 0){
			$("#archetypeArtifactId3").attr('checked', true);
		} else if (window.location.href.indexOf('archetypeArtifactId=quickstart') > 0){
			$("#archetypeArtifactId1").attr('checked', true);
		}

		updateDbTypeDefaultValues();
		updateCommand();
	});
</script>
