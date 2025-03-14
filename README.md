# jenkins-clase

Abrir Jenkins UI
http://<EC2-public-URL>:8080

User: admin
Password:

Conectarse a EC2 (shell)
sudo cat /var/lib/jenkins/secrets/initialAdminPassword 

(usar este password para entrar a Jenkins)

Una vez en Jenkins UI, cambiar password

click en username (admin)
Security
Contraseña, Confirmar, save o apply

Volver al shell de la instancia EC2

sudo visudo
agreagar al final

jenkins ALL=(ALL) NOPASSWD:ALL

Probar que usuario jenkins no pide password para sudo

sudo su - jenkins
sudo cat /etc/shadow

(si pide password, revisar la configuracion)

Volver a Jenkins

Instalar los plugins de AWS en Jenkins

Ir a Manage Jenkins → Plugins.
En la pestaña Available Plugins, buscar e instalar:

- ✅ Pipeline Plugin (si no está instalado ya)
- ✅ Git Plugin
- ✅ Pipeline: SCM Step
- ✅ GitHub Branch Source Plugin
- ✅ GitHub Plugin


Reiniciar Jenkins (Manage Jenkins → Restart Jenkins).

- 🔹 Paso 1: Ir a "Manage Jenkins"

Abre Jenkins y ve a Manage Jenkins.
Haz clic en Manage Credentials.

- 🔹 Paso 2: Agregar Credenciales

Haz clic en "(global)" dentro de la pestaña Stores scoped to Jenkins.

En Global credentials (unrestricted), haz clic en Add Credentials.

🔹 Paso 3: Agregar las credenciales de AWS
Debes crear tres credenciales separadas, una para cada valor (Access Key, Secret Key y Session Token).

🔹 1️⃣ Crear AWS Access Key ID
Tipo: Secret text
Secret: (Pega tu AWS Access Key ID)
ID: aws-access-key
Descripción: AWS Access Key ID
✅ Haz clic en "OK" para guardar.

🔹 2️⃣ Crear AWS Secret Access Key
Tipo: Secret text
Secret: (Pega tu AWS Secret Access Key)
ID: aws-secret-key
Descripción: AWS Secret Access Key
✅ Haz clic en "OK" para guardar.

🔹 3️⃣ Crear AWS Session Token
Tipo: Secret text
Secret: (Pega tu AWS Session Token)
ID: aws-session-token
Descripción: AWS Session Token
✅ Haz clic en "OK" para guardar.

