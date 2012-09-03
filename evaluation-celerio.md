---
layout: french
title: Demande d'évaluation de Celerio
description: Evaluer Celerio en réalisant un prototype 
---

# Evaluation de Celerio

Vous trouverez ici toutes les instructions pour générer __à distance__ un projet et ainsi évaluer le code source généré par Celerio.

Si vous souhaitez acquérir une licence d'utilisation de Celerio <a href="/nous-contacter.html">contactez-nous</a>. 

Pour générer un projet en ligne avec Celerio suivez les instructions ci-dessous.

## Pre requis

* Avoir Java 1.6 ou plus et Maven 2 ou plus installés sur votre machine
* Fournir un email __valide__ (sans ça vous ne pourrez pas générer de projet)


## Etape 1/4: Informations requises pour le reverse et la génération 

<div>
<style>
table#quickstart {
	border: 1px solid gray;
}
table#quickstart tbody tr th {
	border: 1px dotted lightgray;
	font-weight:bold;
}

table#quickstart tbody tr td {
	border: 1px dotted lightgray;
} 

input {
	color: black;
}
input.error {
	margin: 0.5em 0;
	padding: 0;
	background-color: red;
	color: white;
}
</style>
<div id="debug"> </div>
<table id="quickstart">
<form class="form">
	<tbody>
		<tr>
			<th>Email</th>
			<td>
				<input type="text" id="email" class="required updateCommand"> Un email sera envoyé à cette adresse pour la valider.
			</td>
		</tr>		
		<tr>
			<th>Nom du projet</th>
			<td><input type="text" id="artifactId" value="monprojet" placeholder="ex: monprojet" class="required lettersNumbers updateCommand"> Caractères permis: a-z, A-Z, 0-9</td>
		</tr>
		<tr>
			<th style="width:120px;">Package Java racine</th>
			<td><input type="text" id="groupId" size="40" value="com.jaxio.monprojet" placeholder="ex: com.jaxio.demo.monprojet" class="required updateCommand"></td>
		</tr>
		<tr>
			<th>Base de Données</th>
			<td>
				<input type="radio" name="archetypeArtifactId" id="archetypeArtifactId3" value="quickstart-embedded-db-with-configuration" class="updateCommand" checked="checked">
				<label for="archetypeArtifactId3">
					Je préfère utiliser une base de données d'exemple (H2 embarquée).
				</label>
				<br/>
				<input type="radio" name="archetypeArtifactId" id="archetypeArtifactId1" value="quickstart" class="updateCommand">
				<label for="archetypeArtifactId1">Je souhaite que Celerio fasse le reverse de ma propre base de données.  
				</label>
			</td>
		</tr>

		<tr class="jdbc-properties" style="display: none">
			<th>Driver Jdbc</th>
			<td>
				Le reverse de la base de données se fait depuis votre ordinateur grâce à un plugin Maven.
				Pour accéder à votre base de données ce plugin a besoin des informations techniques suivantes:
				<table>
					<tbody>
						<tr>
							<th style="width:120px;"><label for="dbType">Base de Données</label></th>
							<td>
								<select id="dbType" name="dbType" class="updateCommand">
									<option value="mysql">mysql</option>
									<option value="oracle">oracle</option>
									<option value="db2">db2</option>
									<option value="h2">h2</option>
									<option value="postgresql">postgresql</option>
									<option value="other">other</option>
								</select>
								<span id="oracle-database" style="display: none">
								<p class="tip">
									Oracle ne publie pas ses drivers jdbc, vous devez <a href="/en/install-oracle-jdbc-driver-in-maven-repository.html" target="_new">l'installer manuellement dans votre repository Maven</a>.
								</p>
								<p class="tip">
									Si vous dépendez des Sequences Oracle pour générer vos primary key, vous devez configurer Celerio à l'étape 3. 
									Dans la documentation, voir <a href="/documentation/configuration.html#seq_per_table" target="_new">'Use a SEQUENCE per TABLE'</a>. 
								</p>								
								</span>
								<span id="db2-database" style="display: none">
								<p class="tip">
									IBM ne publie pas les drivers jdbc pour DB2, vous devez en <a href="/reverse-db2-database-schema.html" target="_new">installer un manuellement</a>.
								</p>
								</span>
								<span id="other-database" style="display: none">
								<p class="tip">
									Saisissez les informations relatives à votre driver JDBC ci-dessous.
								</p>
								</span>
							</td>
						</tr>
						<tr>
							<th><label for="jdbcGroupId">groupId Maven</label></th>
							<td><input type="text" name="jdbcGroupId" id="jdbcGroupId" size="50" class="updateCommand" placeholder="ex: mysql"></td>
						</tr>
						<tr>
							<th><label for="jdbcArtifactId">artifactId Maven</label></th>
							<td><input type="text" name="jdbcArtifactId" id="jdbcArtifactId" size="50" class="updateCommand" placeholder="ex: mysql-connector-java"></td>
						</tr>
						<tr>
							<th><label for="jdbcGroupId">Version</label></th>
							<td><input type="text" name="jdbcVersion" id="jdbcVersion" class="updateCommand" placeholder="ex: 5.1.17"></td>
						</tr>
						<tr>
							<th><label for="jdbcDriver">Class du Driver</label></th>
							<td><input type="text" name="jdbcDriver" id="jdbcDriver" size="50" class="updateCommand" placeholder="ex: com.mysql.jdbc.Driver"></td>
						</tr>
					</tbody>
				</table>
			</td>
		</tr>
		<tr class="jdbc-properties" style="display: none">
			<th>Accès Jdbc</th>
			<td>
				<p class="tip">
					Attention, n'utilisez surtout pas une base de données de production! Préférez plutôt une base de données dédiée au développement.
					<br/>
					Les informations saisies sont également utilisées dans le projet généré (et donc envoyées à notre serveur) pour accéder à la base de données.
				</p>
				<table>
					<tbody
						<tr>
							<th style="width:120px;"><label for="jdbcUrl">Url</label></th>
							<td><input type="text" name="jdbcUrl" id="jdbcUrl" size="70" class="updateCommand" placeholder="ex: jdbc:mysql://localhost/DBNAME"></td>
						</tr>
						<tr>
							<th><label for="jdbcUser">Utilisateur</label></th>
							<td><input type="text" name="jdbcUser" id="jdbcUser" class="updateCommand" placeholder="ex: admindev"></td>
						</tr>
						<tr>
							<th><label for="jdbcPassword">Mot de passe</label></th>
							<td><input type="text" name="jdbcPassword" id="jdbcPassword" class="updateCommand" placeholder="ex: admindev"></td>
						</tr>
					</tbody>
				</table>
			</td>
		</tr>		
		<tr>
			<th>Type de projet</th>
			<td>
				<table>
					<tr><td><input type="radio" name="frontEnd" id="jsf2Primefaces" value="jsf2Primefaces" class="updateCommand" checked="checked"></td>
						<td width="280">JSF 2, Primefaces 3.3, Spring WebFlow 2.3</td>
						<td>Choix idéal pour les grosses applications de gestion</td></tr>
					<tr><td><input type="radio" name="frontEnd" id="backendJpa" value="backendJpa" class="updateCommand"></td>
						<td>JPA 2 backend only</td>
						<td>Juste le backend...</td></tr>
				</table>
				<p>Tous les projets utilisent Maven 3, JPA 2, Hibernate 4, Spring 3, Spring Security 3, Bean Validation etc.</p>
			</td>
		</tr>
		<tr>
			<th>Derrière un proxy ?</th>
			<td>
				<input type="radio" name="proxyEnable" id="proxyEnableFalse" value="false" class="updateCommand" checked="checked">
				<label for="proxyEnableFalse">No</label>

				<input type="radio" name="proxyEnable" id="proxyEnableTrue" value="true" class="updateCommand">
				<label for="proxyEnableTrue">Yes</label>
				<div class="proxy-properties" style="display: none">
					<table>
						<tbody>
							<tr>
								<th style="width:120px;"><label for="proxyHost">Proxy hostname</label></th>
								<td><input type="text" name="proxyHost" id="proxyHost" size="50" class="updateCommand" value="intranet-proxy"></td>
							</tr>
							<tr>
								<th><label for="proxyPort">Proxy port</label></th>
								<td><input type="text" name="proxyPort" id="proxyPort" size="8" class="updateCommand" value="8080"></td>
							</tr>
							<tr>
								<th><label for="proxyUsername">Username</label></th>
								<td><input type="text" name="proxyUsername" id="proxyUsername" class="updateCommand" placeholder="ex: user"></td>
							</tr>
							<tr>
								<th><label for="proxyPassword">Password</label></th>
								<td><input type="password" name="proxyPassword" id="proxyPassword" class="updateCommand" placeholder="ex: password"></td>
							</tr>
							<tr>
								<th><label for="proxyNtlmDomain">Domain</label></th>
								<td>
									<input type="text" name="proxyNtlmDomain" id="proxyNtlmDomain" class="updateCommand" placeholder="ex: mydomain">
									<i>only if your proxy uses ntlm</i>
								</td>
							</tr>
							<tr>
								<th><label for="proxyNtlmWorkstation">Workstation</label></th>
								<td>
									<input type="text" name="proxyNtlmWorkstation" id="proxyNtlmWorkstation" class="updateCommand" placeholder="ex: mymachine"
										title="Under Windows, 
										<ul>
											<li>Open System by clicking the Start button,</li>
											<li>click on Control Panel,</li>
											<li>click on System and Maintenance,</li>
											<li>click on System.</li>
										</ul>
										You can now find your computer's name">
									<i>only if your proxy uses ntlm</i>
								</td>
							</tr>
						</tbody>
					</table>
					<p class="info">
						Vous devrez très probablement <a href="http://maven.apache.org/guides/mini/guide-proxies.html" target="_new">configurer Maven pour qu'il utilise également le proxy</a> too.
					</p>
				</div>
			</td>
		</tr>
	</tbody>
</form>
</table>
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
		var version= "3.0.78";
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

		$("#groupId").toggleClass("error", !groupId.match(/^([a-zA-Z]+){1}(\.[a-zA-Z0-9_]+)*$/));
		$("#artifactId").toggleClass("error", !artifactId.match(/^[a-zA-Z0-9]+$/));
		$("#email").toggleClass("error", email.length > 0 && !isValidEmailAddress(email));

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
</div>

## Etape 2/4: pre-projet
Copier cette commande dans votre console pour récupérer les quelques fichiers nécessaires aux étapes 3 et 4. 
<textarea id="cmdLine" rows="4" cols="80" readonly="readonly" style="width:850px;height:85px; font-weight: bold"></textarea>

## Etape 3/4:  Configuration de Celerio (Optionnelle)
Vous pouvez <a href="documentation/configuration.html" target="_new">configurer Celerio</a> en éditant un fichier XML. 

## Etape 4/4: Reverse et la génération du code

Copiez-collez ces commandes dans une console pour: 

* réaliser le reverse du schéma votre base de données ou de la base de données H2 d'exemple
* uploader le schéma à notre serveur (seules les méta données sont envoyées) 
* télécharger le projet généré et le dezipper
* déployer le projet en local sur un container Jetty (<a href="http://localhost:8080/">http://localhost:8080/</a>) ou, lancer (si c'est un projet backend) les tests unitaires.

<textarea id="cmdLine2" rows="2" cols="80" readonly="readonly" style="width:850px;height:40px;font-weight: bold"></textarea>

<p class="tip">
	En cas de problème, reportez-vous à cette <a href="/faq.html#question_remote_generation_failed" target="_new">FAQ</a>.
</p>

<p class="tip"> 
	Lors de la première utilisation, ne soyez pas trop surpris par le nombre de dépendences téléchargées par Maven, soyez simplement patient...</a>
</p>

