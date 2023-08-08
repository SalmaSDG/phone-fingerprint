<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Collecte des Caractéristiques du Téléphone</title>
<script>
  async function collectAndSendData() {
    try {
      const model = await getModel();
      const os = navigator.platform;
      const resolution = `${window.screen.width}x${window.screen.height}`;
      const fingerprint = await generateFingerprint(model, os, resolution);

      // Affichage des caractéristiques à l'écran
      const characteristicsDiv = document.getElementById('characteristics');
      characteristicsDiv.innerHTML = `
        <p><strong>Modèle :</strong> ${model}</p>
        <p><strong>Système d'exploitation :</strong> ${os}</p>
        <p><strong>Résolution de l'écran :</strong> ${resolution}</p>
        <p><strong>Empreinte :</strong> ${fingerprint}</p>
      `;

      // Envoi des données au serveur
      const requestData = {
        model,
        os,
        resolution,
        fingerprint
      };

      // Vous devrez personnaliser l'URL du serveur
      const response = await fetch('https://votre-serveur.com/enregistrement', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(requestData)
      });

      const responseBody = await response.json();
      console.log('Réponse du serveur :', responseBody);
    } catch (error) {
      console.error('Erreur :', error);
    }
  }

  async function getModel() {
    try {
      const stream = await navigator.mediaDevices.getUserMedia({ video: true });
      const videoTracks = stream.getVideoTracks();
      const { label } = videoTracks[0].getSettings();
      const model = label.split(' ')[1];
      stream.getTracks().forEach(track => track.stop());
      return model;
    } catch (error) {
      console.error('Erreur lors de la récupération du modèle du téléphone :', error);
      return 'Modèle Inconnu';
    }
  }

  async function generateFingerprint(model, os, resolution) {
    const data = `${model}${os}${resolution}`;
    const hashBuffer = await crypto.subtle.digest('SHA-256', new TextEncoder().encode(data));
    const hashArray = Array.from(new Uint8Array(hashBuffer));
    return hashArray.map(byte => byte.toString(16).padStart(2, '0')).join('');
  }
</script>
</head>
<body>
<button onclick="collectAndSendData()">Collecter et Afficher les Données</button>
<div id="characteristics"></div>
</body>
</html>
