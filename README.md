# Uber Data Engineering GCP Project

## Description

### Objective

This project focuses on building a robust data pipeline and using various technologies to analyze Uber data. It includes integrating with APIs to retrieve the data, creating dimension models, utilizing an open-source data pipeline tool, and leveraging the capabilities of Google Cloud Platform. The main objective is to develop a comprehensive dashboard that provides valuable insights into Uber taxi trip data in NYC.

### Dataset

The dataset used in this project is sourced from NYC OpenData and contains trip records from yellow taxis in NYC for the period of January to June 2016. These records are submitted by Technology Service Providers (TSPs), including Uber, Lyft, and others, operating in New York City. The dataset includes information such as pick-up and drop-off dates/times, locations, trip distances, fares, rate types, payment types, and passenger counts. 

More information could be found here: 
- Website: https://data.cityofnewyork.us/Transportation/2016-Yellow-Taxi-Trip-Data/k67s-dv2t
- Additional information about the API connection: https://dev.socrata.com/foundry/data.cityofnewyork.us/uacg-pexx

### Tools & Technologies

- Language - [Python](https://www.python.org/)
- Cloud - [Google Cloud Platform](https://cloud.google.com/)
- Data Pipeline Tool - [Mage](https://www.mage.ai)
- Storage - [Google Cloud Storage](https://cloud.google.com/storage/)
- Virtual Machine Instance - [Google Compute Engine](https://cloud.google.com/compute)
- Serverless Data Warehouse - [BigQuery](https://cloud.google.com/bigquery/)
- Data visualization - [Looker Studio](https://lookerstudio.google.com/)
- Diagramming application - [Lucidchart](https://lucid.app)

### Data Pipeline Architecture
![Architecture](https://github.com/umidmirzaev/uber/blob/main/images/architecture.jpg)

### Final Result
![Dashboard](https://github.com/umidmirzaev/uber/blob/main/images/Uber_report_page.jpg)
 
## Setup

### Prerequisites

- Create a GCP (Google Cloud Platform) account.
- Register a profile on NYC Open Data platform (https://data.cityofnewyork.us).


### Steps

1. Accessing the 2016 Yellow Taxi Trip Data using the [Socrata Open Data API](https://data.cityofnewyork.us/Transportation/2016-Yellow-Taxi-Trip-Data/k67s-dv2t):
  - Create an App Token in the Developer settings.
  - Use the actual dataset ID from the URL.
  - Connect to the API, authenticate the client, and retrieve the data (you can use Jupyter Notebook).

2. Dimension Modeling:
  - Create a new blank document in Lucid Chart.
  - Use Lucid Chart to convert the flat data into the STAR data model structure with fact and dimensions tables.
  - Prepare the transformation code for dimension modeling (you can use Jupyter Notebook).

3. Upload raw data to Google Cloud Storage:
  - Create a bucket in Google Cloud Storage, ensuring that "Enforce public access prevention on this bucket" is unchecked.
  - Upload the data to the newly created bucket and make it public on the internet.
  - Configure fine-grained access control by going to Permissions and switching from uniform to fine-grained access control.
  - Edit access for the file, add an entry with entity "public", name "allUsers", and access level "Reader".
  - Obtain the generated public URL for the uploaded data.

4. Installing necessary dependencies for runnung Mage and working with Python:
  - In Compute Engine, create an instance with at least evCPU and 16 GB memory, and allow HTTP and HTTPS traffic through the firewall settings.
  - Connect to the instance via SSH.
  - Run the following commands to set up the basic Python environment:
    - `sudo apt-get update -y`
    - `sudo apt-get install python3-distutils`
    - `sudo apt-get install python3-apt`
    - `sudo apt-get install wget`
    - `wget https://bootstrap.pypa.io/get-pip.py`
    - `sudo python3 get-pip.py`
    - `sudo pip3 install pandas`
  
5. Installing Mage in a virtual machine: 
  - Install Mage using pip with the command `sudo pip3 install mage-ai`. 
  - Create a new project with the command `mage start demo_project` with your own project name. 
  - Open the network interface of the virtual machine, create a new firewall rule to accept requests from port 6789 (Mage), and specify the appropriate IP ranges and TCP port number. 
  - Access Mage by entering the external IP address of the network interface in a browser, followed by port number 6789. 

6. Loading and transforming data in Mage: 
  - Create a data loader block "load_uber_data" by selecting "New" -> "Standard Batch" -> "Data loader" -> "Python" -> "API" and provide a file URL from Google Cloud Storage. 
  - To transform the data in Mage, create a transformation block by selecting "Transformer" -> "Python" -> "Generic (no template)", name it as "uber_transformation," and copy the transformation code into the block after loading the data.

7. Loading the data from the dataframes into BigQuery:
  - Make sure you add additional code to the existing transformation block to transform the tables into dictionaries.
  - Create a data exporter block in Mage: Data exporter -> Python -> Google BigQuery. Name the block "uber_big_query_load".
  - In Google Cloud, go to API & Services and navigate to Credentials. Create a new Service Account with BigQuery admin role permissions. Generate a JSON key file and save it on your computer.
  - Go back to Mage and open the `io_config.yaml` file. List all the values available in your saved JSON file using their corresponding keys under `GOOGLE_SERVICE_ACC_KEY`.
  - Open BigQuery, click on "View actions" in your project, create a dataset, and copy the project name and dataset name.
  - Paste the project name and dataset name into the data exporter block in Mage under "table_id".
  - Transform the data back from JSON format to a dataframe.
  - If you encounter the error "ModuleNotFoundError: No module named 'google.cloud'", you need to install the Google Cloud libraries in your instance (VM). Make a new SSH connection and run the following commands:
       - `sudo pip3 install google-cloud`
       - `sudo pip3 install google-cloud-bigquery`
  - Try running the exporter block again to load the data into BigQuery.

8. Create an analytical layer by joining tables in BigQuery.
9. Create a dashboard in Looker Studio:
  - Open Looker Studio and create a blank report.
  - Connect to your Google BigQuery project using the BigQuery connector in Looker.
