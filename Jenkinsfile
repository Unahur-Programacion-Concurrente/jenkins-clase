pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        INSTANCE_TYPE = 't2.micro'
        AMI_ID = 'ami-08b5b3a93ed654d19' // AMI válida
        KEY_NAME = 'laboratorio' // KeyPair existente
        SECURITY_GROUP_NAME = 'JenkinsSecurityGroup'

        // Credenciales AWS
       // Credenciales AWS (mismas que el pipeline de creación)
        AWS_ACCESS_KEY_ID = "mi id"
        AWS_SECRET_ACCESS_KEY = "mi secret"
        AWS_SESSION_TOKEN = "mi token"
        }

    stages {
        stage('Instalar AWS CLI desde Amazon') {
            steps {
                sh '''
                echo "Descargando e instalando AWS CLI desde Amazon..."

                # Eliminar versiones previas
                sudo rm -rf /usr/local/aws-cli awscliv2.zip aws/

                # Descargar AWS CLI v2
                curl -fSL "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" || { echo "Error en la descarga"; exit 1; }

                # Instalar unzip si no está presente
                if ! command -v unzip &> /dev/null; then
                    sudo apt update && sudo apt install -y unzip
                fi

                # Extraer AWS CLI sin confirmaciones
                unzip -o awscliv2.zip || { echo "Error al descomprimir AWS CLI"; exit 1; }

                # Instalar AWS CLI
                sudo ./aws/install || { echo "Error al instalar AWS CLI"; exit 1; }

                # Verificar instalación
                aws --version || { echo "AWS CLI no está instalado correctamente"; exit 1; }
                '''
            }
        }

        stage('Cargar Credenciales AWS Manualmente') {
            steps {
                sh """
                echo "Configurando credenciales AWS manualmente..."
                
                export AWS_ACCESS_KEY_ID="${AWS_ACCESS_KEY_ID}"
                export AWS_SECRET_ACCESS_KEY="${AWS_SECRET_ACCESS_KEY}"
                export AWS_SESSION_TOKEN="${AWS_SESSION_TOKEN}"

                # Verificar autenticación
                aws sts get-caller-identity || { echo "Error en la autenticación de AWS"; exit 1; }
                """
            }
        }

        stage('Crear Security Group si no existe') {
            steps {
                sh """
                echo "Verificando si el Security Group ya existe..."
                SECURITY_GROUP_ID=\$(aws ec2 describe-security-groups --filters "Name=group-name,Values=${SECURITY_GROUP_NAME}" --query "SecurityGroups[0].GroupId" --output text 2>/dev/null)

                if [ "\$SECURITY_GROUP_ID" == "None" ] || [ -z "\$SECURITY_GROUP_ID" ]; then
                    echo "Security Group no existe. Creándolo..."
                    SECURITY_GROUP_ID=\$(aws ec2 create-security-group --group-name ${SECURITY_GROUP_NAME} --description "Security group para Jenkins EC2" --query 'GroupId' --output text)
                    
                    echo "Security Group creado: \$SECURITY_GROUP_ID"

                    # Agregar reglas para SSH y HTTP
                    aws ec2 authorize-security-group-ingress --group-id \$SECURITY_GROUP_ID --protocol tcp --port 22 --cidr 0.0.0.0/0
                    aws ec2 authorize-security-group-ingress --group-id \$SECURITY_GROUP_ID --protocol tcp --port 80 --cidr 0.0.0.0/0
                else
                    echo "Security Group ya existe con ID: \$SECURITY_GROUP_ID"
                fi

                echo "\$SECURITY_GROUP_ID" > security_group_id.txt
                """
            }
        }

        stage('Crear Instancia EC2') {
            steps {
                sh """
                echo "Creando instancia EC2..."
                SECURITY_GROUP_ID=\$(cat security_group_id.txt)

                INSTANCE_ID=\$(aws ec2 run-instances --image-id ${AMI_ID} --instance-type ${INSTANCE_TYPE} --key-name ${KEY_NAME} --security-group-ids \$SECURITY_GROUP_ID --query 'Instances[0].InstanceId' --output text)

                echo "Instancia creada: \$INSTANCE_ID"
                echo "\$INSTANCE_ID" > instance_id.txt
                """
            }
        }

        stage('Mostrar Información de la Instancia') {
            steps {
                sh """
                echo "Obteniendo información de la instancia..."
                INSTANCE_ID=\$(cat instance_id.txt)
                aws ec2 describe-instances --instance-ids \$INSTANCE_ID --query 'Reservations[*].Instances[*].{ID:InstanceId,State:State.Name,PublicIP:PublicIpAddress}' --output table
                """
            }
        }
    }
}
