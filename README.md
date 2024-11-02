# AI-ShopAdvisor  
🛍️ **AI-Powered E-Commerce Recommendation System**  

A robust, production-ready recommendation engine built with Python and Django that leverages collaborative filtering (ALS) and deep learning (Neural CF) algorithms (TensorFlow/PyTorch) to deliver personalized product suggestions. Coupled with PostgreSQL for data management and Superset for interactive dashboards, AI-ShopAdvisor helps e-commerce platforms enhance user engagement and drive data-backed decision-making.  

---

## 🌟 Key Features  

- **Personalized Recommendations**  
  - Collaborative filtering (ALS) for latent-factor modeling  
  - Neural collaborative filtering using TensorFlow or PyTorch  
  - Real-time inference endpoints to fetch user-specific suggestions  

- **Data Management & Visualization**  
  - PostgreSQL for robust relational data storage  
  - Superset dashboards to monitor key metrics (e.g., precision@K, recall@K, CTR)  
  - SQL-based queries to compute user behavior summaries, product popularity, etc.  

- **End-to-End Pipeline**  
  - Django REST Framework (DRF) for API development  
  - Scheduled ETL jobs on AWS (e.g., Lambda + S3 or ECS tasks) to preprocess clickstreams  
  - Scikit-learn for feature engineering (e.g., user embedding preprocessing, train/test splits)  

- **AWS-Powered Deployment**  
  - Dockerized services running on AWS ECS/EKS  
  - Terraform scripts to provision RDS (PostgreSQL), S3 buckets for model artifacts, and IAM roles  
  - CI/CD pipelines (GitHub Actions) to build, test, and deploy both backend and ML workflows  

---

## 📦 Tech Stack  

Component: Backend Framework  
Technology: Python, Django, Django REST Framework  

Component: ML Libraries  
Technology: TensorFlow (Neural CF), PyTorch (optional), Scikit-learn (feature engineering)  

Component: Database  
Technology: PostgreSQL (AWS RDS)  

Component: Data Visualization  
Technology: Apache Superset  

Component: Cloud Infrastructure  
Technology: AWS (ECS/EKS, S3, RDS, Lambda, IAM)  

Component: Infrastructure as Code  
Technology: Terraform  

Component: CI/CD  
Technology: GitHub Actions  

Component: Frontend (Demo UI, optional)  
Technology: TypeScript, React (Next.js)  

---

## 📁 Repository Structure  

AI-ShopAdvisor/
- backend/
  - manage.py
  - shopadvisor/ (Django project folder)
    - settings.py
    - urls.py
    - wsgi.py
  - recommendations/ (Django app for recommendation logic)
    - models.py (Models: User, Product, Interaction, Feedback)
    - serializers.py (DRF serializers)
    - views.py (API endpoints: /recommendations/, /feedback/)
    - urls.py
    - ml/ (ML pipeline code)
      - train_als.py
      - train_neural_cf.py
      - preprocess.py (Data cleaning & feature engineering)
      - inference.py (Loading trained models & generating predictions)
    - utils.py (Utility functions, e.g., evaluation metrics)
  - requirements.txt

- infra/ (Terraform configurations)
  - main.tf
  - variables.tf
  - outputs.tf

- dashboards/ (Superset configuration & SQL queries)
  - superset_config.py
  - sql_queries/
    - user_metrics.sql
    - product_popularity.sql

- demos/ (Frontend demo, optional)
  - UI/ (Next.js + TypeScript app)
    - pages/
    - components/
    - package.json
  - docker-compose.yml

- data/ (Example data & scripts)
  - sample_users.csv
  - sample_products.csv
  - sample_interactions.csv

- README.md (You are here)

---

## 🛠️ Getting Started  

Follow these steps to get a local development environment running. By the end, you’ll have:
1. A running Django REST API that can generate and serve recommendations.
2. A Superset instance connected to the same PostgreSQL database to visualize key performance metrics.

### 1. Prerequisites  
- Python 3.8+  
- Node.js 16+ (if running the frontend demo)  
- Docker & Docker Compose (optional but recommended for Superset and local Postgres)  
- AWS CLI configured (for deploying infra and S3 access)  
- Terraform 1.0+ (for IaC if deploying to AWS)  

---

### 2. Clone the Repository  
Use git to clone the repository and navigate into it:  
- git clone https://github.com/<your-org>/AI-ShopAdvisor.git  
- cd AI-ShopAdvisor  

---

### 3. Backend Setup (Django + ML)  

1. Create & Activate a Virtual Environment  
   - cd backend  
   - python -m venv venv  
   - source venv/bin/activate         # Linux/macOS  
   - .\venv\Scripts\activate          # Windows PowerShell  

2. Install Python Dependencies  
   - pip install --upgrade pip  
   - pip install -r requirements.txt  

3. Configure Environment Variables  
   Create a .env file in /backend/ (loaded via django-environ or python-dotenv) with:  
   - DEBUG=True  
   - SECRET_KEY=your-secret-key  
   - ALLOWED_HOSTS=localhost,127.0.0.1  

   - DATABASE_URL=postgresql://<db_user>:<db_password>@localhost:5432/ai_shopadvisor  

   - AWS_ACCESS_KEY_ID=YOUR_AWS_ACCESS_KEY  
   - AWS_SECRET_ACCESS_KEY=YOUR_AWS_SECRET  
   - AWS_DEFAULT_REGION=us-west-2  
   - S3_BUCKET_NAME=ai-shopadvisor-models  

   - ALS_FACTORS=50  
   - NEURAL_EMBEDDING_SIZE=64  
   - TRAIN_TEST_SPLIT=0.8  

4. Apply Migrations & Create Superuser  
   - python manage.py migrate  
   - python manage.py createsuperuser  

5. Generate Sample Data (Optional)  
   To load example CSV files:  
   - python manage.py loaddata data/sample_users.csv  
   - python manage.py loaddata data/sample_products.csv  
   - python manage.py loaddata data/sample_interactions.csv  

   Or run the command:  
   - python manage.py generate_sample_data  

6. Train the Models  
   - ALS Model:  
     python recommendations/ml/train_als.py --input_csv data/sample_interactions.csv --save_path models/als_model.pkl --factors $ALS_FACTORS  
   - Neural Collaborative Filtering Model:  
     python recommendations/ml/train_neural_cf.py --input_csv data/sample_interactions.csv --epochs 10 --batch_size 512 --save_path models/neural_cf_model.h5  

7. Start the Django Development Server  
   - python manage.py runserver  

---

### 4. Superset Setup (Data Visualization)  

Note: Alternatively, run Superset via Docker Compose.

1. Initialize Superset (assuming installed apache-superset in same venv)  
   - superset db upgrade  
   - export FLASK_APP=superset  
   - superset fab create-admin --username superset_admin --firstname Superset --lastname Admin --email admin@localhost --password admin123  
   - superset init  

2. Configure Database Connection in dashboards/superset_config.py (SQLALCHEMY_DATABASE_URI matching DATABASE_URL)  

3. Run Superset  
   - superset run -p 8088 --with-threads --reload --debugger  
   - Access at http://localhost:8088 (login superset_admin/admin123)  

4. Import Dashboards & Charts  
   Use SQL Lab to run SQL queries in dashboards/sql_queries/ to create charts and dashboards for:  
   - User Engagement Metrics  
   - Model Performance (Precision@K, Recall@K over time)  
   - Product Popularity (Top 10 products)  

---

### 5. Frontend Demo (Optional)  

A minimal React/Next.js demo is provided under demos/UI/:

1. Navigate to the UI folder:  
   - cd demos/UI  

2. Install Dependencies:  
   - npm install  

3. Configure Environment Variables (.env.local):  
   - NEXT_PUBLIC_API_BASE_URL=http://localhost:8000/api  

4. Run Development Server:  
   - npm run dev  
   - Access at http://localhost:3000 (enter a valid user_id)  

---

## 🔑 API Endpoints  

- GET /api/recommendations/<user_id>/ : Retrieve top-N personalized recommendations  
- GET /api/recommendations/<user_id>/?model=als&k=10 : Top-10 ALS recommendations  
- GET /api/recommendations/<user_id>/?model=neural&k=20 : Top-20 Neural CF recommendations  
- POST /api/feedback/ : Submit user feedback (JSON payload with user_id, product_id, rating, timestamp)  
- GET /api/debug/generate-data/ : Regenerate sample users, products, interactions (returns new IDs)  
- GET /api/debug/users/ : List all sample users  
- GET /api/debug/products/ : List all sample products  

Note: When DEBUG=False, endpoints require token authentication (use Django Admin to create tokens or implement JWT).  

---

## 🧪 Testing & Validation  

1. Unit Tests:  
   - python manage.py test recommendations  

2. Integration Tests (Optional using pytest and pytest-django):  
   - Seed database, train ALS model, query /api/recommendations/, validate response format  

3. ML Model Evaluation:  
   - Evaluation metrics saved under models/metrics/ (als_eval.json, neural_eval.json)  
   - Use Superset to visualize trends by uploading JSON files into Postgres table model_metrics  

---

## 📈 Monitoring & Performance  

- Caching:  
  - Use Django’s cache framework (e.g., Redis) for frequently requested recommendation lists (TTL 10 minutes)  

- Timeout Handling:  
  - ML inference endpoints have default timeout of 5 seconds  
  - Retraining jobs executed on AWS Batch/ECS with CloudWatch monitoring  

- Database Optimizations:  
  - Index on (user_id, interaction_timestamp) for Interaction model  
  - Materialized view recent_interactions_mv computed nightly for batch feature engineering  

- Superset Dashboards:  
  - User Activity Dashboard (DAU, sessions, avg. interactions)  
  - Recommendation Quality Dashboard (Precision@K, Recall@K, NDCG over time)  
  - Product Popularity Dashboard (Top 20 clicked/purchased products)  

---

## 🛠️ Deployment (AWS)  

1. Infrastructure Provisioning (Terraform) from infra/ directory:  
   - cd infra  
   - terraform init  
   - terraform plan -out=plan.out  
   - terraform apply plan.out  

   Creates:  
   - AWS RDS (PostgreSQL) instance  
   - S3 Bucket for model artifacts  
   - ECS Cluster with Task Definitions  
   - IAM Roles & Policies for ECS tasks  

2. Build & Push Docker Images (assuming ECR repositories exist):  
   - cd backend  
   - docker build -t ai-shopadvisor-api:latest .  
   - docker tag ai-shopadvisor-api:latest <AWS_ACCOUNT_ID>.dkr.ecr.us-west-2.amazonaws.com/ai-shopadvisor-api:latest  
   - docker push <AWS_ACCOUNT_ID>.dkr.ecr.us-west-2.amazonaws.com/ai-shopadvisor-api:latest  

3. Update ECS Service Definitions (update infra/ecs Task Definition to new image tags)  
   - terraform apply -auto-approve  

4. CI/CD Pipeline (GitHub Actions) sample:  
   - Trigger on pushes to main, run tests, build & push Docker images, update Terraform state  

---

## 🤝 Contributing  

1. Fork the repository  
2. Create a feature branch: git checkout -b feature/<your-feature-name>  
3. Install dependencies and run tests locally  
4. Commit changes and push to fork  
5. Open a Pull Request against main  

### Code Style & Guidelines  
- Backend: Follow PEP 8 conventions  
- ML Scripts: Use docstrings, keep modules under 200 lines  
- Terraform: Use terraform fmt and terraform validate  
- Frontend: Adhere to Airbnb TypeScript Style Guide  

---

## 📚 Additional Resources  

- Django REST Framework Docs: https://www.django-rest-framework.org/  
- TensorFlow Guide (Neural CF): https://www.tensorflow.org/recommenders  
- Apache Superset Docs: https://superset.apache.org/docs/intro  
- PostgreSQL Best Practices: https://www.postgresql.org/docs/current/performance-tips.html  
- AWS ECS/ECR Tutorial: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/docker-basics.html  

---


Thank you for using AI-ShopAdvisor! We look forward to your feedback and contributions.
