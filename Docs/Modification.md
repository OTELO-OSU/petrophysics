# Modification du formulaire  

**Formulaire de données:**

Le formulaires de données à été construit à partir du Framework JS Jsonform (https://github.com/jsonform/jsonform)
ce qui permet de moduler le formulaire selon le projet a deployer.
Il y a un fichier form_general qui definit les champs obligatoire à tout les projets.
Afin de permettre un deploiement pour plusieurs projet facilement, les champs unique a un projet sont definit dans un fichier specifique au projet.
Il faut que le fichier respecte la regle de nommage form_PROJECTNAME.twig.

Exemple: Le formulaire du projet petrophysics est definie dans le fichier /backend/src/geosamples/frontend/templates/form_petrophysics.twig



**Conception du fichier JSON:**

On distingue trois grande partie dans ce fichier de construction du formulaire:

- schema ( definit la structure à utiliser, on definit un type, un titre et ici une liste avec enum) 
	exemple :
	
		"core": {
		      "type": "string",
		      "title": "Core",
		      "enum": [ "Yes", "No" ]
		    },
	    
- form ( definit le comportement du champs dans le formulaire, on definit le type de champs html, et ici un event change avec du JS) 
	exemple: 
	
		"key": "core",
		"type":"radios",
		 "onChange": function (evt) {
			var value = $(evt.target).val();
			if (value=='Yes') {
			$('.form-group.jsonform-error-core_depth').removeClass("hidden field");
			$('.form-group.jsonform-error-core_azimut').removeClass("hidden field");
			$('.form-group.jsonform-error-core_dip').removeClass("hidden field");
			}
			else{
			$('.form-group.jsonform-error-core_depth').addClass("hidden field");
			$('.form-group.jsonform-error-core_azimut').addClass("hidden field");
			$('.form-group.jsonform-error-core_dip').addClass("hidden field");
			}
		      }
		},


- value (permet de charger des valeurs precedemment entré (utilisé pour le chargement de templates, ou l'édition))
exemple:
	
		"core":"{{core}}", ({{core}} etant une variable twig)

On peut aussi effectuer un chargement à la volée en JS pour une templates de données CSV: 
	dans le fichier upload.html.twig : 
	
	function handlefileselect:  

	if (value == "CORE") {
								   
																if(values[1].toUpperCase()=='YES'){
																	var radios = $('input:radio[name=core]');
																	radios.filter('[value=Yes]').prop('checked', true);
																	$('.form-group.jsonform-error-core_depth').removeClass("hidden field");

	$('.form-group.jsonform-error-core_azimut').removeClass("hidden field");
																	$('.form-group.jsonform-error-core_dip').removeClass("hidden field");
																	$("input[name='core_depth']").val(values[2]);
																         $("input[name='core_azimut']").val(values[3]);
																        $("input[name='core_dip']").val(values[4]);
								   
   
   	}else{
   	var radios = $('input:radio[name=core]');
   	radios.filter('[value=No]').prop('checked', true);
	}
	}


	

**Gestion des measurements par projet:**
La liste de measurements est gérée au niveau du fichier projet via la variable twig list_measurements.

Pour la définir, il suffit de la declarer dans le fichier JSON projet comme ceci:

	{%set list_measurements%}
		["Select abbreviation","BGC",
		"EMPA",
		"LAICPMS",]
	{%endset%}

Dans le fichier upload.html.twig, la fonction switch_measurement() définit la nature et l'unité de chaque measurement pour un projet definit.

Exemple: 

		{%if collection_name == 'scandium'%}
			switch (value) {
			case 'BGC':
				nature="BULK_GEOCHEM" ;
				unit="%WT_AND_PPMWT" ;
				break;
			case 'EMPA':
				nature="ELECTRONIC_MICROPROBE" ;
				unit="%WT" ;
				break;
			case 'LAICPMS':
				nature="LA-ICP-MS" ;
				unit="PPMWT" ;
				break;

			}

		{%endif%}


# Modification des filtres de recherche <a name="modif_recherche"></a>

Les champs des filtres de recherches peuvent etre modifiés, pour cela, il faut modifier le constructeur de la Classe PHP Backend/src/geosamples/backend/controller/RequestController tels que :

	   $this->filter1   = "INTRO.SUPPLEMENTARY_FIELDS.HOST_LITHOLOGY_OR_PROTOLITH";
	   $this->filter2   = "INTRO.SUPPLEMENTARY_FIELDS.LITHOLOGY_3";
   
 Il ne faut pas oublier d'appliquer ces changements dans le JS, Backend/src/js/index.js ligne 510:
   
	   if (k.SUPPLEMENTARY_FIELDS.HOST_LITHOLOGY_OR_PROTOLITH != null && k.SUPPLEMENTARY_FIELDS.HOST_LITHOLOGY_OR_PROTOLITH != '') {
	   lithology[k.SUPPLEMENTARY_FIELDS.HOST_LITHOLOGY_OR_PROTOLITH] = (k.SUPPLEMENTARY_FIELDS.HOST_LITHOLOGY_OR_PROTOLITH);
	}



	if (k.SUPPLEMENTARY_FIELDS.LITHOLOGY_3 != null && k.SUPPLEMENTARY_FIELDS.LITHOLOGY_3 != '') {
	lithology3[k.SUPPLEMENTARY_FIELDS.LITHOLOGY_3] = (k.SUPPLEMENTARY_FIELDS.LITHOLOGY_3);
	}
  
  Pensez à effacer votre cache pour tester.


# Modification spécifique projet ACEV

Le projet ACEV a besoin de section de code spécifique pour fonctionner, ces sections de code sont identifié par un commentaire "//ACEV". 
Pour les activer, il suffit de les décommenter, et commenter l'ancien code.
Voici où elles se trouvent:	

		Backend/src/js/index js ligne 500
		Backend/src/geosamples/backend/controller/RequestController.php ligne 266 et 376







