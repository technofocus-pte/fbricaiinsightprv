# **Use Case 06- Identifying and extracting text with Document Intelligence in Microsoft Fabric**
**Introduction**

Analyzing structured data has been an easy process for some time but the
same cannot be said for unstructured data. Unstructured data, such as
text, images, and videos, is more difficult to analyze and interpret.
However, with the advent of advanced AI models, such as OpenAI's GPT-3
and GPT-4, it is now becoming easier to analyze and gain insights from
unstructured data.

An example of such analysis is the ability to query a document for
specific information using natural language which is achievable though a
combination of information retrieval and language generation.

By leveraging the RAG (Retrieval-Augmented Generation) framework, you
can create a powerful question-and-answering pipeline that uses a large
language model (LLM) and you own data to generate responses.

The architecture of such an application is as shown below:

  ![](./media/image1.png)

**Objective**

- Create a multi-service resource for Azure AI services using Azure
  portal

- To create fabric capacity and workspace, Key vault, and fabric
  workspace

- Pre-process PDF Documents using Azure AI Document Intelligence in
  Azure AI Services.

- Perform text chunking using SynapseML.

- Generate embeddings for the chunks using SynapseML and Azure OpenAI
  Services.

- Store the embeddings in Azure AI Search.

- Build a question answering pipeline.

# **Exercise 1: Environment Setup**

## Task 1: Create a multi-service resource for Azure AI services

he multi-service resource is listed under **Azure AI
services** \> **Azure AI services multi-service account** in the portal.
To create a multi-service resource follow these instructions:

1.  Select this link to create a multi-service
    resource: +++https://portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne+++

2.  On the **Create** page, provide the following information:

    |Project details	|Description|
    |-------|-------|
    |Subscription |Select one of your available Azure subscriptions	|
    |Resource group |Click on Create new> enter AI-FabricXX(XX can be a unique number)|
    |Region |Select the appropriate region for your CognitiveServices. In this lab, we have chosen the East US 2 region.	|
    |Name|+++Cognitive-serviceXXX+++( XXX can be a unique number, you can add more digits after XXX to make the name unique)|
    |Pricing tier|Standard S0

3.  Configure other settings for your resource as needed, read and
    accept the conditions (as applicable), and then select **Review +
    create**.

     ![](./media/image2.png)

     ![](./media/image3.png)

4.  In the **Review+submit** tab, once the Validation is Passed, click
    on the **Create** button.

      ![](./media/image4.png)
  
5.  After the deployment is completed, click on the **Go to resource**
    button.

     ![](./media/image5.png)

6.  In your **Azure** **AI service** window, navigate to the **Resource
    Management** section, and click on **Keys and Endpoints**.

     ![](./media/image6.png)

7.  In **Keys and Endpoints** page, copy **KEY1, KEY 2,** and
    **Endpoint** values and paste them in a notepad as shown in the
    below image, then **Save** the notepad to use the information in the
    upcoming tasks.

     ![](./media/image7.png)

## **Task 2: Create a key vault using the Azure portal**

1.  In Azure portal home page, click on **+ Create Resource**.

     ![](./media/image8.png)

2.  In the **Create a resource** page search bar, type **Key vault** and
    click on the appeared **Key vault** .
     ![](./media/image9.png)

4.  Click on **Key Vault** section.

      ![](./media/image10.png)

4.  On the **Create a key Vault** page, provide the following
    information and click on **Review+create** button.

    |Field	|Description|
    |-------|-------|
    |Subscription |Select your Azure OpenAI subscription	|
    |Resource group Select your Resource group(that you have created in Task 1)	|
    |Region |EastUS 2	|
    |Name |+++fabrickeyvaultXX+++(XXcan be unique number)|
    |Pricing Tier |Click on change Price Tire>select Standard |

5.  Once the Validation is passed, click on the **Create**
    button.
    ![](./media/image11.png)

     ![](./media/image12.png)

6.  After the deployment is completed, click on the **Go to resource**
    button.

      ![](./media/image13.png)

7.  In your **fabrickeyvaultXX** window, from the left menu, click on
    the **Access control(IAM).**

8.  On the Access control(IAM) page, Click +**Add** and select **Add
    role assignments.**

     ![](./media/image14.png)

9.  In **Job function roles,** type the **+++Key vault administrator+++**
    in the search box and select it. Click **Next**

     ![](./media/image15.png)

10.  In the **Add role assignment** tab, select Assign access to User
    group or service principal. Under Members, click **+Select members**

     ![](./media/image16.png)

11.  On the Select members tab, search your Azure OpenAI subscription and
    click **Select.**

      ![](./media/image17.png)

12.  In the **Add role assignment** page, Click **Review + Assign**, you
    will get a notification once the role assignment is complete.

      ![](./media/image18.png)
 
      ![](./media/image19.png)

13.  You will see a notification – added as Azure AI Developer for
    Azure-openai-testXX

     ![](./media/image20.png)

## Task 3: Create a secret using Azure Key vault

1.  On the Key Vault left-hand sidebar, select **Objects** then
    select **Secrets**.

    ![](./media/image21.png)

2.  Select **+ Generate/Import**.

    ![](./media/image22.png)

3.  On the **Create a secret** page, provide the following information
    and click on **Create** button .

    |	||
    |-------|-------|
    |Upload options |Manual	|
    |Name |Enter the name +++aisearchkey+++	|
    |Secret Value |password321	|

     ![](./media/image23.png)

4.  Select **+ Generate/Import**.

     ![](./media/image24.png)

5.  On the **Create a secret** page, provide the following information
    and click on **Create** button .

    |	|  |
    |-------|-------|
    |Upload options |Manual	|
    |Name |Enter the name +++aiservicekey+++	|
    |Secret Value |password321	|
     ![](./media/image25.png)
     ![](./media/image26.png)

6.  In **Key vault** page, copy **Key vault** name, and **Secrets**
    values and paste them in a notepad as shown in the below image, then
    **Save** the notepad to use the information in the upcoming tasks.

     ![](./media/image27.png)

## **Task 4: Create an Azure AI Search service in the portal**

1.  In Azure portal home page, click on **+ Create Resource**.

      ![](./media/image8.png)

2.  In the **Create a resource** page search bar, type **Azure AI
    Search** and click on the appeared **azure ai search**.

      ![](./media/image28.png)

3.  Click on **azure ai search** section.

      ![](./media/image29.png)

4.  In the **Azure AI Search** page, click on the **Create** button.

      ![](./media/image30.png)

5.  On the **Create a search service** page, provide the following
    information and click on **Review+create** button.

    |Field	|Description|
    |------|------|
    |Subscription	| Select your Azure OpenAI subscription |
    |Resource group	| Select your Resource group(that you have created )|
    |Region | EastUS 2 |
    |Name	| mysearchserviceXX  (XXcan be unique number)|
    |Pricing Tier	| Click on change Price Tire>select Basic|
    
     ![](./media/image31.png)

     ![](./media/image32.png)

      ![](./media/image33.png)

6.  Once the Validation is passed, click on the **Create** button.

     ![](./media/image34.png)

7.  After the deployment is completed, click on the **Go to resource**
    button.

     ![](./media/image35.png)

8.  copy **AI search name** and paste them in a notepad as shown in the
    below image, then **Save** the notepad to use the information in the
    upcoming lab.

    ![](./media/image36.png)

## Task 5: Create Fabric Capacity and Workspace

Microsoft Fabric is deployed to an Azure Active Directory tenant. Within
each Fabric tenant, Fabric capacities can be created to group resources
for various purposes -- this might be done organizationally (sales,
marketing, development), geographically, or other logical grouping.

If a Fabric Trial is available, we recommend taking advantage of that
opportunity to try Microsoft Fabric for a period of time (currently 60
days) with no commitment. To see if you are in a trial or eligible for a
trial, visit the [Fabric portal](https://app.fabric.microsoft.com/). If
you are able to log in or presented the option to start a trial, you
should be all set!

To create a Fabric capacity outside of a trial environment, create a new
resource from the Azure portal, and search for Fabric.

1.  From the Azure portal home page, click on **Azure portal menu**
    represented by three horizontal bars on the left side of the
    Microsoft Azure command bar as shown in the below image.

    ![](./media/image37.png)

2.  Navigate and click on **+ Create a resource**.

     ![](./media/image38.png)

3.  On **Create a resource** page, in the **Search services and
    marketplace** search bar, type+++ **Fabric+++**, then select
    **Microsoft fabric**.

      ![](./media/image39.png)

4.  In the **Marketplace** page, navigate to the **Microsoft Fabric**
    section, click on the Create button dropdown, then select
    **Microsoft Fabric** as shown in the image.

     ![](./media/image40.png)

5.  In the **Create Fabric capacity** window, under the **Basics** tab,
    enter the following details and click on the **Review+create**
    button.

    |  |  |
    |-----|-----|
    |Subscription	|Select the assigned subscription|
    |Resource group	|Select your Resource group(that you have created in Task 1)|
    |Capacity name	|+++fabriccapacity45+++( XXX can be a unique number, you can add more digits after XXX to make the name unique)|
    |Region	|Select West US 3|
    |Size	|Click on Change size> select F4 SKU and click on Select button|

      ![](./media/image41.png)
      ![](./media/image42.png)
      ![](./media/image43.png)

6.  In the **Review+submit** tab, once the Validation is Passed, click
    on the **Create** button.

      ![](./media/image44.png)
 
      ![](./media/image45.png)

7.  After the deployment is completed, click on the **Go to resource**
    button.

     ![](./media/image46.png)
 
     ![](./media/image47.png)

## **Task 6: Create a Fabric workspace**

In this task, you create a Fabric workspace. The workspace contains all
the items needed for this lakehouse tutorial, which includes lakehouse,
dataflows, Data Factory pipelines, the notebooks, Power BI datasets, and
reports.

1.  Open your browser, navigate to the address bar, and type or paste
    the following URL: +++https://app.fabric.microsoft.com/+++ then press the
    **Enter** button.

      ![](./media/image48.png)

2.  In the **Microsoft Fabric** window, enter your **Microsoft 365**
    credentials, and click on the **Submit** button.

      ![](./media/image49.png)

3.  Then, In the **Microsoft** window enter the password and click on
    the **Sign in** button**.**

      ![](./media/image50.png)

4.  In **Stay signed in?** window, click on the **Yes** button.

      ![](./media/image51.png)

5.  You’ll be directed to Power BI Home page.

      ![](./media/image52.png)

6. Go back to **Power BI** window. On the left side navigation menu of
    Power BI Home page, navigate and click on **Workspaces**.

     ![](./media/image53.png)

7. In the Workspaces pane, click on **+** **New workspace** button**.**

     ![](./media/image54.png)

8. In the **Create a workspace** pane that appears on the right side,
    enter the following details, and click on the **Apply** button.
   
    |  |  |
    |-----|------|
    |Name	|+++Document Intelligence-FabricXX+++(XXX can be a unique number)|
    |Advanced	|Select Fabric Capacity|
    |Capacity |	Select Realtimefabriccapacity-West US 3|

     ![](./media/image55.png)
     ![](./media/image56.png)
     ![](./media/image57.png)
13. Wait for the deployment to complete. It takes 2-3 minutes to
    complete.
      ![](./media/image58.png)

## **Task 7: Create a lakehouse**

1.  In the **Document Intelligence-FabricXX** page, click on the **Power
    BI** icon located at the bottom left and select **Data Engineering**
    under Synapse.

       ![](./media/image59.png)

2.  In the **Synapse** **Data Engineering** **Home** page,
    select **Lakehouse** to create a lakehouse.

      ![](./media/image60.png)

3.  In the **Synapse Data Engineering Home** page, select **Lakehouse**
    to create a lakehouse.

      ![](./media/image61.png)

4.  In the **New lakehouse** dialog box, enter !!**data_lakehouse**!! in
    the **Name** field, click on the **Create** button and open the new
    lakehouse.

  **Note**: Ensure to remove space before **data_lakehouse**.
      ![](./media/image62.png)

5.  You will see a notification stating **Successfully created SQL
    endpoint**.

      ![](./media/image63.png)

      ![](./media/image64.png)

# **Exercise 2: Loading and Pre-processing PDF Documents **

## **Task 1: Configure Azure API keys**

To begin, navigate back to the rag_workshop Lakehouse in your workspace
and create a new notebook by selecting Open Notebook and selecting New
Notebook from the options.

1.  In the **Lakehouse** page, navigate and click on **Open notebook**
    drop in the command bar, then select **New notebook**.

     ![](./media/image65.png)

     ![](./media/image66.png)

2.  In the query editor, paste the following code.  Provide the keys for
    Azure AI Services, Azure Key Vault name and secrets to access the
    services

     **Copy**
    ```
    # Azure AI Search
    AI_SEARCH_NAME = ""
    AI_SEARCH_INDEX_NAME = "rag-demo-index"
    AI_SEARCH_API_KEY = ""
    
    # Azure AI Services
    AI_SERVICES_KEY = ""
    AI_SERVICES_LOCATION = ""
    ```

    ![](./media/image67.png)

## Task 2: Loading & Analyzing the Document

1.  we will be using a specific document
    named [**support.pdf**](https://github.com/Azure-Samples/azure-openai-rag-workshop/blob/main/data/support.pdf) which
    will be the source of our data.

2.  To download the document, use the **+ Code** icon below the cell
    output to add a new code cell to the notebook, and enter the
    following code in it. Click on **▷ Run cell** button and review the
    output

    **Copy**
    ```
    import requests
    import os
    
    url = "https://github.com/Azure-Samples/azure-openai-rag-workshop/raw/main/data/support.pdf"
    response = requests.get(url)
    
    # Specify your path here
    path = "/lakehouse/default/Files/"
    
    # Ensure the directory exists
    os.makedirs(path, exist_ok=True)
    
    # Write the content to a file in the specified path
    filename = url.rsplit("/")[-1]
    with open(os.path.join(path, filename), "wb") as f:
        f.write(response.content)
    ```

     ![](./media/image68.png)

3.  Now, load the PDF document into a Spark DataFrame using the
    spark.read.format("binaryFile") method provided by Apache Spark

4.  Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output

    **Copy**
    ```
    from pyspark.sql.functions import udf
    from pyspark.sql.types import StringType
    document_path = f"Files/{filename}"
    df = spark.read.format("binaryFile").load(document_path).select("_metadata.file_name", "content").limit(10).cache()
    display(df)
    ```
    ![](./media/image69.png)

This code will read the PDF document and create a Spark DataFrame
named df with the contents of the PDF. The DataFrame will have a schema
that represents the structure of the PDF document, including its textual
content.

5.  Next, we'll use the Azure AI Document Intelligence to read the PDF
    documents and extract the text from them.

6.  Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output

    **Copy**
    ```
    from synapse.ml.services import AnalyzeDocument
    from pyspark.sql.functions import col
    
    analyze_document = (
        AnalyzeDocument()
        .setPrebuiltModelId("prebuilt-layout")
        .setSubscriptionKey(AI_SERVICES_KEY)
        .setLocation(AI_SERVICES_LOCATION)
        .setImageBytesCol("content")
        .setOutputCol("result")
    )
    
    analyzed_df = (
        analyze_document.transform(df)
        .withColumn("output_content", col("result.analyzeResult.content"))
        .withColumn("paragraphs", col("result.analyzeResult.paragraphs"))
    ).cache()
    ```

     ![](./media/image70.png)

7.  We can observe the analyzed Spark DataFrame named analyzed_df using
    the following code. Note that we drop the content column as it is
    not needed anymore.

8.  Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output

    **Copy**
    ```
    analyzed_df = analyzed_df.drop("content")
    display(analyzed_df)
    ```

  ![](./media/image71.png)

# Exercise 3: Generating and Storing Embeddings

## **Task 1: Text Chunking**

Before we can generate the embeddings, we need to split the text into
chunks. To do this we leverage SynapseML’s PageSplitter to divide the
documents into smaller sections, which are subsequently stored in
the chunks column. This allows for more granular representation and
processing of the document content.

1.  Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output

    **Copy**
    ```
    from synapse.ml.featurize.text import PageSplitter
    
    ps = (
        PageSplitter()
        .setInputCol("output_content")
        .setMaximumPageLength(4000)
        .setMinimumPageLength(3000)
        .setOutputCol("chunks")
    )
    
    splitted_df = ps.transform(analyzed_df)
    display(splitted_df)
    ```
     ![](./media/image72.png)

Note that the chunks for each document are presented in a single row
inside an array. In order to embed all the chunks in the following
cells, we need to have each chunk in a separate row.

2.  Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output

    **Copy**
    ```
    from pyspark.sql.functions import posexplode, col, concat
    
    # Each "chunks" column contains the chunks for a single document in an array
    # The posexplode function will separate each chunk into its own row
    exploded_df = splitted_df.select("file_name", posexplode(col("chunks")).alias("chunk_index", "chunk"))
    
    # Add a unique identifier for each chunk
    exploded_df = exploded_df.withColumn("unique_id", concat(exploded_df.file_name, exploded_df.chunk_index))
    
    display(exploded_df)
    ```

  ![](./media/image73.png)

From this code snippet we first explode these arrays so there is only
one chunk in each row, then filter the Spark DataFrame in order to only
keep the path to the document and the chunk in a single row.

## Task 2: Generating Embeddings

Next we'll generate the embeddings for each chunk. To do this we utilize
both SynapseML and Azure OpenAI Service. By integrating the built in
Azure OpenAI service with SynapseML, we can leverage the power of the
Apache Spark distributed computing framework to process numerous prompts
using the OpenAI service.

1.  Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output

    **Copy**
    ```
    from synapse.ml.services import OpenAIEmbedding
    
    embedding = (
        OpenAIEmbedding()
        .setDeploymentName("text-embedding-ada-002")
        .setTextCol("chunk")
        .setErrorCol("error")
        .setOutputCol("embeddings")
    )
    
    df_embeddings = embedding.transform(exploded_df)
    
    display(df_embeddings)
    ```

  ![](./media/image74.png)

This integration enables the SynapseML embedding client to generate
embeddings in a distributed manner, enabling efficient processing of
large volumes of data

## Task 3: Storing Embeddings 

[Azure AI
Search](https://learn.microsoft.com/azure/search/search-what-is-azure-search?WT.mc_id=data-114676-jndemenge) is
a powerful search engine that includes the ability to perform full text
search, vector search, and hybrid search. For more examples of its
vector search capabilities, see the [azure-search-vector-samples
repository](https://github.com/Azure/azure-search-vector-samples/).

Storing data in Azure AI Search involves two main steps:

**Creating the index:** The first step is to define the schema of the
search index, which includes the properties of each field as well as any
vector search strategies that will be used.

**Adding chunked documents and embeddings:** The second step is to
upload the chunked documents, along with their corresponding embeddings,
to the index. This allows for efficient storage and retrieval of the
data using hybrid and vector search.

1.  The following code snippet demonstrates how to create an index in
    Azure AI Search using the Azure AI Search REST API. This code
    creates an index with fields for the unique identifier of each
    document, the text content of the document, and the vector embedding
    of the text content.

2.  Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output

    **Copy**
    ```
    import requests
    import json
    
    # Length of the embedding vector (OpenAI ada-002 generates embeddings of length 1536)
    EMBEDDING_LENGTH = 1536
    
    # Create index for AI Search with fields id, content, and contentVector
    # Note the datatypes for each field below
    url = f"https://{AI_SEARCH_NAME}.search.windows.net/indexes/{AI_SEARCH_INDEX_NAME}?api-version=2023-11-01"
    payload = json.dumps(
        {
            "name": AI_SEARCH_INDEX_NAME,
            "fields": [
                # Unique identifier for each document
                {
                    "name": "id",
                    "type": "Edm.String",
                    "key": True,
                    "filterable": True,
                },
                # Text content of the document
                {
                    "name": "content",
                    "type": "Edm.String",
                    "searchable": True,
                    "retrievable": True,
                },
                # Vector embedding of the text content
                {
                    "name": "contentVector",
                    "type": "Collection(Edm.Single)",
                    "searchable": True,
                    "retrievable": True,
                    "dimensions": EMBEDDING_LENGTH,
                    "vectorSearchProfile": "vectorConfig",
                },
            ],
            "vectorSearch": {
                "algorithms": [{"name": "hnswConfig", "kind": "hnsw", "hnswParameters": {"metric": "cosine"}}],
                "profiles": [{"name": "vectorConfig", "algorithm": "hnswConfig"}],
            },
        }
    )
    headers = {"Content-Type": "application/json", "api-key": AI_SEARCH_API_KEY}
    
    response = requests.request("PUT", url, headers=headers, data=payload)
    if response.status_code == 201:
        print("Index created!")
    elif response.status_code == 204:
        print("Index updated!")
    else:
        print(f"HTTP request failed with status code {response.status_code}")
        print(f"HTTP response body: {response.text}")
    ```

      ![](./media/image75.png)
      ![](./media/image76.png)

3.  The next step is to upload the chunks to the newly created Azure AI
    Search index. The Azure AI Search REST API supports up to 1000
    "documents" per request. Note that in this case, each of our
    "documents" is in fact a chunk of the original file

4.  Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output

    **Copy**
    ```
    import re
    
    from pyspark.sql.functions import monotonically_increasing_id
    
    
    def insert_into_index(documents):
        """Uploads a list of 'documents' to Azure AI Search index."""
    
        url = f"https://{AI_SEARCH_NAME}.search.windows.net/indexes/{AI_SEARCH_INDEX_NAME}/docs/index?api-version=2023-11-01"
    
        payload = json.dumps({"value": documents})
        headers = {
            "Content-Type": "application/json",
            "api-key": AI_SEARCH_API_KEY,
        }
    
        response = requests.request("POST", url, headers=headers, data=payload)
    
        if response.status_code == 200 or response.status_code == 201:
            return "Success"
        else:
            return f"Failure: {response.text}"
    
    def make_safe_id(row_id: str):
        """Strips disallowed characters from row id for use as Azure AI search document ID."""
        return re.sub("[^0-9a-zA-Z_-]", "_", row_id)
    
    
    def upload_rows(rows):
        """Uploads the rows in a Spark dataframe to Azure AI Search.
        Limits uploads to 1000 rows at a time due to Azure AI Search API limits.
        """
        BATCH_SIZE = 1000
        rows = list(rows)
        for i in range(0, len(rows), BATCH_SIZE):
            row_batch = rows[i : i + BATCH_SIZE]
            documents = []
            for row in rows:
                documents.append(
                    {
                        "id": make_safe_id(row["unique_id"]),
                        "content": row["chunk"],
                        "contentVector": row["embeddings"].tolist(),
                        "@search.action": "upload",
                    },
                )
            status = insert_into_index(documents)
            yield [row_batch[0]["row_index"], row_batch[-1]["row_index"], status]
    
    # Add ID to help track what rows were successfully uploaded
    df_embeddings = df_embeddings.withColumn("row_index", monotonically_increasing_id())
    
    # Run upload_batch on partitions of the dataframe
    res = df_embeddings.rdd.mapPartitions(upload_rows)
    display(res.toDF(["start_index", "end_index", "insertion_status"]))
    ```

  ![](./media/image77.png)

  ![](./media/image78.png)

# Exercise 4: Retrieving Relevant Documents and Answering Questions

After processing the document, we can proceed to pose a question. We
will
use [SynapseML](https://microsoft.github.io/SynapseML/docs/Explore%20Algorithms/OpenAI/Quickstart%20-%20OpenAI%20Embedding/) to
convert the user's question into an embedding and then utilize cosine
similarity to retrieve the top K document chunks that closely match the
user's question.

## Task 1: Configure Environment & Azure API Keys

Create a new notebook in the Lakehouse and save it as rag_application.
We'll use this notebook to build the RAG application.

1.  Provide the credentials for access to Azure AI Search. You can copy
    the values from the from Azure Portal.(Exercise 1\>Task 4)

2.  Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output

    Copy
    ```
    # Azure AI Search
    AI_SEARCH_NAME = ''
    AI_SEARCH_INDEX_NAME = 'rag-demo-index'
    AI_SEARCH_API_KEY = ''
    ```

      ![](./media/image79.png)

3.  The following function takes a user's question as input and converts
    it into an embedding using the text-embedding-ada-002 model. This
    code assumes you're using the Pre-built AI Services in Microsoft
    Fabric

4.  Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output

    **Copy**
    ```
    def gen_question_embedding(user_question):
        """Generates embedding for user_question using SynapseML."""
        from synapse.ml.services import OpenAIEmbedding
    
        df_ques = spark.createDataFrame([(user_question, 1)], ["questions", "dummy"])
        embedding = (
            OpenAIEmbedding()
            .setDeploymentName('text-embedding-ada-002')
            .setTextCol("questions")
            .setErrorCol("errorQ")
            .setOutputCol("embeddings")
        )
        df_ques_embeddings = embedding.transform(df_ques)
        row = df_ques_embeddings.collect()[0]
        question_embedding = row.embeddings.tolist()
        return question_embedding
    ```
  ![](./media/image80.png)

## Task 2: Retrieve Relevant Documents

1.  The next step is to use the user question and its embedding to
    retrieve the top K most relevant document chunks from the search
    index. The following function retrieves the top K entries using
    hybrid search

2.  Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output

    **Copy**
    ```
    import json 
    import requests
    
    def retrieve_top_chunks(k, question, question_embedding):
        """Retrieve the top K entries from Azure AI Search using hybrid search."""
        url = f"https://{AI_SEARCH_NAME}.search.windows.net/indexes/{AI_SEARCH_INDEX_NAME}/docs/search?api-version=2023-11-01"
    
        payload = json.dumps({
            "search": question,
            "top": k,
            "vectorQueries": [
                {
                    "vector": question_embedding,
                    "k": k,
                    "fields": "contentVector",
                    "kind": "vector"
                }
            ]
        })
    
        headers = {
            "Content-Type": "application/json",
            "api-key": AI_SEARCH_API_KEY,
        }
    
        response = requests.request("POST", url, headers=headers, data=payload)
        output = json.loads(response.text)
        return output
    ```
     ![](./media/image81.png)

With those functions defined, we can define a function that takes a
user's question, generates an embedding for the question, retrieves the
top K document chunks, and concatenates the content of the retrieved
documents to form the context for the user's question.

3.  Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output

    **Copy**
    ```
    def get_context(user_question, retrieved_k = 5):
        # Generate embeddings for the question
        question_embedding = gen_question_embedding(user_question)
    
        # Retrieve the top K entries
        output = retrieve_top_chunks(retrieved_k, user_question, question_embedding)
    
        # concatenate the content of the retrieved documents
        context = [chunk["content"] for chunk in output["value"]]
    
        return context
    ```

  ![](./media/image82.png)

## **Task 3: Answering the User's Question**

Finally, we can define a function that takes a user's question,
retrieves the context for the question, and sends both the context and
the question to a large language model to generate a response. For this
demo, we'll use the gpt-35-turbo-16k, a model that is optimized for
conversation.

1.  Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output

    **Copy**
    ```
    from pyspark.sql import Row
    from synapse.ml.services.openai import OpenAIChatCompletion
    
    
    def make_message(role, content):
        return Row(role=role, content=content, name=role)
    
    def get_response(user_question):
        context = get_context(user_question)
    
        # Write a prompt with context and user_question as variables 
        prompt = f"""
        context: {context}
        Answer the question based on the context above.
        If the information to answer the question is not present in the given context then reply "I don't know".
        """
    
        chat_df = spark.createDataFrame(
            [
                (
                    [
                        make_message(
                            "system", prompt
                        ),
                        make_message("user", user_question),
                    ],
                ),
            ]
        ).toDF("messages")
    
        chat_completion = (
            OpenAIChatCompletion()
            .setDeploymentName("gpt-35-turbo-16k") # deploymentName could be one of {gpt-35-turbo, gpt-35-turbo-16k}
            .setMessagesCol("messages")
            .setErrorCol("error")
            .setOutputCol("chat_completions")
        )
    
        result_df = chat_completion.transform(chat_df).select("chat_completions.choices.message.content")
    
        result = []
        for row in result_df.collect():
            content_string = ' '.join(row['content'])
            result.append(content_string)
    
        # Join the list into a single string
        result = ' '.join(result)
        
        return result
    ```

      ![](./media/image83.png)
      ![](./media/image84.png)

2.  Now, we can call that function with an example question to see the
    response:

3.  Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output

    **Copy**
    ```
    user_question = "how do i make a booking?"
    response = get_response(user_question)
    print(response)
    ```

  ![](./media/image85.png)

## Task 4: Delete the resources

To avoid incurring unnecessary Azure costs, you should delete the
resources you created in this quickstart if they're no longer needed. To
manage resources, you can use the [Azure
portal](https://portal.azure.com/?azure-portal=true).

1.  To delete the storage account, navigate to **Azure portal Home**
    page, click on **Resource groups**.

     ![](./media/image86.png)

2.  Click on the assigned resource group.

      ![](./media/image87.png)

3.  In the **Resource group** home page, select the **delete resource
    group**

      ![](./media/image88.png)

4.  In the **Delete Resources** pane that appears on the right side,
    navigate to **Enter “resource group name” to confirm deletion**
    field, then click on the **Delete** button.

      ![](./media/image89.png)

5.  On **Delete confirmation** dialog box, click on **Delete** button.

      ![](./media/image90.png)

6.  Click on the bell icon, you’ll see the notification –**Deleted
    resource group AOAI-RG8**

      ![](./media/image91.png)

7.  Open your browser, navigate to the address bar, and type or paste
    the following URL: +++https://app.fabric.microsoft.com/+++ then
    press the **Enter** button.

      ![](./media/image92.png)

8.  Select the **...** option under the workspace name and
    select **Workspace settings**.

      ![](./media/image93.png)

9.  Select **General** and click on **Remove this workspace.**

      ![](./media/image94.png)

10. Click on **Delete** in the warning that pops up.

     ![](./media/image95.png)

11. Wait for a notification that the Workspace has been deleted, before
    proceeding to the next lab.

     ![](./media/image96.png)
