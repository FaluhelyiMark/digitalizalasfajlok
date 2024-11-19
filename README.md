<!DOCTYPE html>
<html lang="hu">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Videó Digitalizálás</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            background-color: #f9f9f9;
            color: #333;
        }

        header {
            background-color: #0062cc;
            color: white;
            padding: 20px;
            text-align: center;
        }

        .upload-container {
            margin: 20px auto;
            padding: 20px;
            max-width: 600px;
            background: white;
            border: 1px solid #ddd;
            border-radius: 8px;
            text-align: center;
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
        }

        input, button {
            margin: 10px 0;
            padding: 10px;
            font-size: 16px;
        }

        button {
            background-color: #0062cc;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }

        button:hover {
            background-color: #0056b3;
        }

        .file-preview {
            margin-top: 20px;
        }

        video {
            max-width: 100%;
            display: block;
            margin: 10px auto;
        }

        .hidden {
            display: none;
        }
    </style>
</head>
<body>
<header>
    <h1>Videó Digitalizálás</h1>
</header>
<div class="upload-container">
    <h2>Videó feltöltése</h2>
    <label>
        <input type="checkbox" id="enableUpload"> Engedélyezze a feltöltést
    </label>
    <div id="codeSection" class="hidden">
        <input type="password" id="uploadCode" placeholder="Adja meg a kódot">
        <button id="verifyCodeButton">Ellenőrzés</button>
    </div>
    <div id="uploadSection" class="hidden">
        <input type="file" id="videoFile" accept="video/*">
        <input type="text" id="uniqueCode" placeholder="Adjon meg egy egyedi kódot">
        <button id="uploadButton">Feltöltés</button>
    </div>

    <h3>Fájl visszakeresése</h3>
    <input type="text" id="retrieveCode" placeholder="Adja meg az egyedi kódot">
    <button id="retrieveButton">Keresés</button>

    <div class="file-preview" id="filePreview" style="display: none;">
        <h3>Talált fájl:</h3>
        <video id="videoPreview" controls></video>
        <a id="downloadLink" download>
            <button>Letöltés</button>
        </a>
        <button id="deleteButton" class="delete-btn">Törlés</button>
    </div>
</div>

<script>
    const storageKey = "videoStorage";
    const correctCode = "2013";

    // Feltöltés engedélyezése
    document.getElementById('enableUpload').addEventListener('change', function() {
        const codeSection = document.getElementById('codeSection');
        const uploadSection = document.getElementById('uploadSection');
        if (this.checked) {
            codeSection.classList.remove('hidden');
            uploadSection.classList.add('hidden');
        } else {
            codeSection.classList.add('hidden');
            uploadSection.classList.add('hidden');
        }
    });

    // Kód ellenőrzése
    document.getElementById('verifyCodeButton').addEventListener('click', () => {
        const uploadCode = document.getElementById('uploadCode').value.trim();
        const uploadSection = document.getElementById('uploadSection');

        if (uploadCode === correctCode) {
            alert("Kód helyes! Feltöltés engedélyezve.");
            uploadSection.classList.remove('hidden');
            document.getElementById('codeSection').classList.add('hidden');
        } else {
            alert("Helytelen kód! Kérjük, próbálja újra.");
        }
    });

    // Feltöltött fájlok mentése helyi tárhelyre
    document.getElementById('uploadButton').addEventListener('click', () => {
        const fileInput = document.getElementById('videoFile');
        const codeInput = document.getElementById('uniqueCode');
        const file = fileInput.files[0];
        const uniqueCode = codeInput.value.trim();

        if (!file || !uniqueCode) {
            alert("Kérjük, töltsön fel egy fájlt és adjon meg egy egyedi kódot!");
            return;
        }

        const reader = new FileReader();
        reader.onload = () => {
            const videoData = reader.result;

            // Helyi tárhelybe mentés
            const storedVideos = JSON.parse(localStorage.getItem(storageKey)) || {};
            storedVideos[uniqueCode] = videoData;
            localStorage.setItem(storageKey, JSON.stringify(storedVideos));

            alert("Fájl sikeresen mentve!");
            fileInput.value = "";
            codeInput.value = "";
        };

        reader.readAsDataURL(file);
    });

    // Feltöltött fájl visszakeresése
    document.getElementById('retrieveButton').addEventListener('click', () => {
        const retrieveCode = document.getElementById('retrieveCode').value.trim();
        const storedVideos = JSON.parse(localStorage.getItem(storageKey)) || {};

        if (!storedVideos[retrieveCode]) {
            alert("Nem található fájl az adott kóddal!");
            return;
        }

        const videoData = storedVideos[retrieveCode];
        const videoPreview = document.getElementById('videoPreview');
        const downloadLink = document.getElementById('downloadLink');
        const deleteButton = document.getElementById('deleteButton');

        videoPreview.src = videoData;
        downloadLink.href = videoData;
        downloadLink.download = `${retrieveCode}.mp4`;

        // Törlés gomb működés
        deleteButton.onclick = () => {
            delete storedVideos[retrieveCode];
            localStorage.setItem(storageKey, JSON.stringify(storedVideos));
            alert("A fájl sikeresen törölve!");
            document.getElementById('filePreview').style.display = 'none';
        };

        document.getElementById('filePreview').style.display = 'block';
    });
</script>
</body>
</html>
