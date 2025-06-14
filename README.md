# Node.js Web Service on Google Cloud Platform

A simple Node.js web service built with Express.js and deployed to Google Cloud Run using GitHub Actions and Docker.

## Features

- RESTful API with Express.js
- Docker containerization
- Automated CI/CD with GitHub Actions
- Deployment to Google Cloud Run
- Health check endpoint
- Graceful shutdown handling
- Security best practices

## API Endpoints

- `GET /` - Welcome message
- `GET /health` - Health check endpoint

## Local Development

### Prerequisites

- Node.js 18+ installed
- npm or yarn package manager
- Docker (optional, for local container testing)

### Setup

1. Install dependencies:
```bash
npm install
```

2. Start the development server:
```bash
npm run dev
```

3. The service will be available at `http://localhost:8080`

### Testing with Docker locally

1. Build the Docker image:
```bash
docker build -t nodejs-gcp-service .
```

2. Run the container:
```bash
docker run -p 8080:8080 nodejs-gcp-service
```

## Deployment to Google Cloud Platform

### Prerequisites

1. **Google Cloud Project**: Create a GCP project at [Google Cloud Console](https://console.cloud.google.com/)
2. **GitHub Repository**: Push your code to a GitHub repository
3. **Google Cloud Services**: Enable the following APIs in your GCP project:
   - Cloud Run API
   - Container Registry API
   - Cloud Build API (optional)

### Step-by-Step Deployment Guide

#### Step 1: Set up Google Cloud Project

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project or select an existing one
3. Note your PROJECT_ID

#### Step 2: Enable Required APIs

```bash
# Enable required APIs
gcloud services enable run.googleapis.com
gcloud services enable containerregistry.googleapis.com
```

#### Step 3: Create Service Account

1. Go to IAM & Admin > Service Accounts
2. Click "Create Service Account"
3. Name: `github-actions-deployer`
4. Grant the following roles:
   - Cloud Run Admin
   - Storage Admin
   - Container Registry Service Agent

#### Step 4: Generate Service Account Key

1. Click on the created service account
2. Go to "Keys" tab
3. Click "Add Key" > "Create new key"
4. Choose JSON format
5. Download the key file

#### Step 5: Configure GitHub Secrets

Go to your GitHub repository settings > Secrets and variables > Actions, and add:

1. `GCP_PROJECT_ID`: Your Google Cloud Project ID
2. `GCP_SA_KEY`: The entire contents of the JSON key file

#### Step 6: Deploy

1. Push your code to the `main` or `master` branch
2. GitHub Actions will automatically:
   - Run tests
   - Build Docker image
   - Push to Google Container Registry
   - Deploy to Cloud Run

### Manual Deployment (Alternative)

If you prefer to deploy manually:

1. Authenticate with Google Cloud:
```bash
gcloud auth login
gcloud config set project YOUR_PROJECT_ID
```

2. Build and push the Docker image:
```bash
docker build -t gcr.io/YOUR_PROJECT_ID/nodejs-gcp-service .
docker push gcr.io/YOUR_PROJECT_ID/nodejs-gcp-service
```

3. Deploy to Cloud Run:
```bash
gcloud run deploy nodejs-gcp-service \
  --image gcr.io/YOUR_PROJECT_ID/nodejs-gcp-service \
  --platform managed \
  --region us-central1 \
  --allow-unauthenticated \
  --port 8080
```

## Environment Variables

- `PORT`: Server port (default: 8080)
- `NODE_ENV`: Environment (development/production)

## Monitoring and Logs

- **Cloud Run Console**: Monitor your service at [Cloud Run Console](https://console.cloud.google.com/run)
- **Logs**: View logs in Google Cloud Logging
- **Metrics**: Monitor performance metrics in the Cloud Run dashboard

## Cost Optimization

Cloud Run pricing is based on:
- CPU and memory usage
- Number of requests
- Networking

The service is configured with:
- Minimum instances: 0 (scales to zero when not used)
- Maximum instances: 10
- Memory: 512Mi
- CPU: 1

## Security Features

- Non-root user in Docker container
- Multi-stage Docker build
- Health checks
- Proper error handling
- HTTPS by default on Cloud Run

## Troubleshooting

### Common Issues

1. **Build fails**: Check Docker syntax and dependencies
2. **Deployment fails**: Verify GCP permissions and API enablement
3. **Service not accessible**: Check Cloud Run service configuration
4. **GitHub Actions fails**: Verify secrets are correctly set

### Debugging

1. Check GitHub Actions logs
2. Review Cloud Run logs in GCP Console
3. Test Docker image locally
4. Verify service account permissions

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test locally
5. Submit a pull request

## License

MIT License 