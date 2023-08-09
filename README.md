<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Collecte des Caractéristiques du Téléphone</title>
<script>
  // Fonction principale pour collecter et afficher les caractéristiques
  async function collectAndDisplayData() {
    try {
      // Récupération du modèle du téléphone
      const model = await getModel();

      // Récupération du système d'exploitation
      const os = navigator.platform;

      // Récupération de la résolution de l'écran
      const resolution = `${window.screen.width}x${window.screen.height}`;

      // Génération de l'empreinte à partir des caractéristiques
      const fingerprint = await generateFingerprint(model, os, resolution);

      // Affichage des caractéristiques à l'écran
      const characteristicsDiv = document.getElementById('characteristics');
      characteristicsDiv.innerHTML = `
        <p><strong>Modèle :</strong> ${model}</p>
        <p><strong>Système d'exploitation :</strong> ${os}</p>
        <p><strong>Résolution de l'écran :</strong> ${resolution}</p>
        <p><strong>Empreinte :</strong> ${fingerprint}</p>
      `;
      
      // Envoi des données au serveur (si nécessaire)
      // Remplacez l'URL par l'URL de votre serveur
      // await sendDataToServer(model, os, resolution, fingerprint);
    } catch (error) {
      console.error('Erreur :', error);
    }
  }

  // Fonction pour récupérer le modèle du téléphone
  async function getModel() {
    try {
      // Utilisation de l'User Agent pour obtenir des informations sur le modèle
      const userAgent = window.navigator.userAgent;
      
      // Recherche de modèles courants dans l'User Agent
      const modelKeywords = ['iPhone', 'Samsung', 'Techno', 'Android', 'Xiaomi', 'Huawei'];
      const foundKeyword = modelKeywords.find(keyword => userAgent.includes(keyword));
      
      if (foundKeyword) {
        return foundKeyword;
      } else {
        throw new Error('Modèle du téléphone non identifié dans l\'user agent.');
      }
    } catch (error) {
      console.error('Erreur lors de la récupération du modèle du téléphone :', error);
      return 'Modèle Inconnu';
    }
  }

  // Fonction pour générer l'empreinte unique
  async function generateFingerprint(model, os, resolution) {
    const data = `${model}${os}${resolution}`;
    const hashBuffer = await crypto.subtle.digest('SHA-256', new TextEncoder().encode(data));
    const hashArray = Array.from(new Uint8Array(hashBuffer));
    return hashArray.map(byte => byte.toString(16).padStart(2, '0')).join('');
  }

  // Fonction pour envoyer les données au serveur (si nécessaire)
  async function sendDataToServer(model, os, resolution, fingerprint) {
    const requestData = {
      model,
      os,
      resolution,
      fingerprint
    };

    const response = await fetch('https://salmasdg.github.io/phone-fingerprint/', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(requestData)
    });

    const responseBody = await response.json();
    console.log('Réponse du serveur :', responseBody);
  }
</script>
</head>
<body>
<button onclick="collectAndDisplayData()">Collecter et Afficher les Données</button>
<div id="characteristics"></div>
</body>
</html>
