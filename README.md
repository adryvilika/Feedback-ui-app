# Serverless Feedback Web App on AWS

Un'applicazione web serverless completa progettata per raccogliere feedback dagli utenti (inclusi allegati PDF). Il progetto dimostra l'implementazione pratica di un'architettura Cloud event-driven su AWS, l'utilizzo dei servizi serverless per abbattere i costi di gestione e l'automazione dei rilasci tramite una pipeline CI/CD.

## Features Principali

* **Modulo di Feedback Interattivo:** Frontend responsive in HTML/CSS/Vanilla JS che raccoglie Nome, Email, Messaggio e converte gli allegati PDF in Base64 prima dell'invio.
* **Database NoSQL:** I dati strutturati (metadati) vengono salvati in modo sicuro e veloce su **Amazon DynamoDB**.
* **Storage Sicuro:** I file PDF allegati vengono decodificati dal backend e archiviati in un bucket **Amazon S3** privato.
* **Notifiche Real-time:** Invio automatico di un'email di conferma o di alert amministrativo tramite **Amazon SES**.
* **Backend Serverless:** Gestione della logica di business affidata ad **AWS Lambda** (Python) richiamata da **API Gateway**.
* **Hosting Globale e Veloce:** Il frontend è ospitato staticamente su S3 e distribuito globalmente a bassa latenza tramite la CDN **Amazon CloudFront** (HTTPS integrato).
* **Deploy Automatizzato (CI/CD):** Ogni push sul branch `main` innesca una pipeline **GitHub Actions** che aggiorna il bucket S3 e invalida la cache di CloudFront.

## Architettura del Sistema

```text
[ Utente Web ] 
      │ (Visita il sito HTTPS)
      ▼
[ Amazon CloudFront ] ──(Richiede file statici)──► [ Amazon S3 (Hosting Bucket) ]
      │ 
      │ (Compila form e invia POST)
      ▼
[ Amazon API Gateway ] ──(Endpoint /submit)
      │
      ▼
[ AWS Lambda (Python) ] 
      ├── 💾 Salva metadati record ──► [ Amazon DynamoDB ]
      ├── 📁 Carica file PDF ────────► [ Amazon S3 (Storage Bucket) ]
      └── 📧 Invia notifica ─────────► [ Amazon SES ]
```


## Tecnologie Utilizzate
Frontend: HTML5, CSS3, Vanilla JavaScript

**Backend & Compute**: AWS Lambda (Python 3.x), Amazon API Gateway (REST)

**Storage & Database**: Amazon S3, Amazon DynamoDB

**Networking & Content Delivery**: Amazon CloudFront

**Comunicazione**: Amazon SES (Simple Email Service)

**Sicurezza**: AWS IAM (Identity and Access Management)

**DevOps**: Git, GitHub Actions

## Struttura del Progetto
Plaintext
.
├── .github/
│   └── workflows/
│       └── deploy.yml      # Pipeline CI/CD per il deploy automatico
├── index.html              # Frontend UI e logica form (Fetch API + Base64)
└── README.md               # Documentazione del progetto

## Come replicare questo progetto (Deployment Steps)
1️⃣ **Creazione S3 Buckets**
Un bucket per l'hosting del frontend (con Static Website Hosting abilitato).

Un bucket per salvare i PDF dei feedback (con blocco totale dell'accesso pubblico).

2️⃣ **Configurazione DynamoDB**
Nome Tabella: FeedbackTable (o nome a scelta).

Partition key: feedback_id (String).

3️⃣ **Setup Amazon SES**
Verifica l'indirizzo email dell'amministratore (o mittente/destinatario) nella console SES per autorizzare l'invio delle notifiche.

4️⃣ **Sviluppo AWS Lambda (Python)**
Crea una funzione Lambda che riceve il payload, decodifica il PDF in Base64, lo carica su S3, salva i dati del form su DynamoDB e invia una mail tramite SES.

Permessi IAM fondamentali: Assicurati di allegare al ruolo della Lambda le policy per s3:PutObject, dynamodb:PutItem e ses:SendEmail.

5️⃣ **Configurazione API Gateway**
Crea una REST API con una risorsa /submit e un metodo POST.

Abilita l'integrazione Proxy con la funzione Lambda.

Fondamentale: Abilita il CORS per consentire le chiamate dal dominio del tuo frontend.

6️⃣ **Setup CI/CD con GitHub Actions**
Crea un utente IAM su AWS dedicato al deploy, con permessi minimi (scrittura su S3 e invalidazione CloudFront).

Genera le chiavi d'accesso e salvale nei Secrets del tuo repository GitHub:

AWS_ACCESS_KEY_ID

AWS_SECRET_ACCESS_KEY

AWS_REGION

S3_BUCKET (Nome del bucket di hosting)

CLOUDFRONT_DIST_ID (ID della distribuzione)



