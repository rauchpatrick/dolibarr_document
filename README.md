# Dolibarr : Ajout de CGV dans les propositions commerciales et les factures

*Date de validation : 21 mars 2024*
*Testée sur la V19.0.1*


## 1. Dépot du PDF

Il faut mettre le fichier cgv.pdf dans /documents/mycompany

## 2. Utilisation des modèles

Modification des modèles originaux :
- pour les propales /htdocs/core/modules/propale/doc/pdf_cyan.modules.php  
- pour les factures /htdocs/core/modules/facture/doc/pdf_sponge.modules.php  

**Convention de nommage des modèles :**

- Pour les propales : bleu
- Pour les factures : concombre

Une fois les CGV déposées dans le dossier comme indiqué au Chapitre 1, il faut déposer les modèles dans les dossiers suivants :

- pdf_bleu_cgv.modules.php => /htdocs/core/modules/propale/doc/  
- pdf_concombre.modules.php => /htdocs/core/modules/facture/doc/  

Une fois les deux fichiers déposés, il faut les activer :

- Accueil - Configuration - Modules/Applications - Propositions commerciales
- Accueil - Configuration - Modules/Applications - Factures

Les deux modèles sont modifiés de la même manière. Chaque ajout de code commence par // COMPR afin de permettre un déplacement rapide dans le fichier.

## 3. Modification des modèles

Ci-dessous se trouvent listées les modifications apportées aux modèles d'origines. Il est inutile de créer ces modifications si vous utilisez directement les deux modèles (bleu et concombre) fournis dans ce dépot. 
Cette procédure est uniquement indiquée pour expliquer ce qui a été fait dans le code.  

**A. Ajout d'une variable**

Dans la section public function __construct($db)  

Rajouter

```
	// COMPR Déclaration de la variable pour ajouter les CGV au pdf de base
	$this->pdf_modele_cgv="cgv.pdf";
```

**B. Appel de la fonction**

Juste avant `$pdf->Close();` au même niveau d'indentation rajouter :

```
// COMPR appel de la fonction pour ajouter les CGV
	$this->ajoutCGV($pdf,$object);
```	

**C. Ajout de la fonction**

Tout en bas du code, avant la dernière } coller la fonction suivante :

```
	// Fonction qui permet d'ajouter les CGV en complément des pages de la proposition commerciale
	//COMPR
	protected function ajoutCGV(&$pdf,$object)
	{
		if (file_exists(DOL_DATA_ROOT."/mycompany/".$this->pdf_modele_cgv)) {
			$pagecount = $pdf->setSourceFile(DOL_DATA_ROOT."/mycompany/".$this->pdf_modele_cgv);
			for ($i = 1; $i <= $pagecount; $i++) {
					$tplidx = $pdf->ImportPage($i);
					$s = $pdf->getTemplatesize($tplidx);
					$pdf->AddPage('P', array($s, $s));
					$pdf->useTemplate($tplidx);
			}
		}
	}
```
