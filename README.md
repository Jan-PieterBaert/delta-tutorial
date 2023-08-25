# Delta tutorial

Welcome to the Delta Producer/Consumer Tutorial! In this comprehensive guide, we will walk you through the setup and configuration of the powerful Delta Producer and Consumer stacks. These essential components facilitate efficient data synchronization, allowing smooth data consumption between applications.

Here's a brief outline of the tutorial:

**Step 0: Setup of the producer and consumer stack** Step 0 introduces two application stacks, one of which will be used on the producer side and the other will be used on the consumer side.

**Step 1: Setting up the Delta Producer Stack** Step 1 introduces the Delta Producer stack, guiding you through the setup and configuration of its various components. We will cover essential aspects like mu-authorization layer, delta-notifier, job-controller, and background jobs.

**Step 2: Activating the Delta Producer Stack** After configuring the Delta Producer stack, step 2 focuses on its activation, ensuring that all components are ready to generate and publish delta messages.

**Step 3: Configuring the Delta Consumer Stack** In step 3, we introduce the Delta Consumer stack, explaining its purpose and guiding you through its configuration. We will look at the mu-authorization layer and delta-consumer service.

**Step 4: Linking the Consumer and Producer Stacks** Step 4 demonstrates how to link the Delta Consumer with the Delta Producer stack. We will cover the necessary configurations to enable data consumption and synchronization between the two.

**Step 5: Implementing Producer-Side Authentication** In step 5, we focus on enhancing security by implementing producer-side authentication. You will learn how to generate and add keys to ensure secure data publication.

**Step 6: Showcasing File Sharing Abilities** Finally, in step 6, we showcase the file sharing abilities of the Delta Producer stack, allowing efficient sharing and utilization of data files.

As part of this tutorial, we use a books frontend, the code for which can be found at https://github.com/Jan-PieterBaert/delta-tutorial-books-frontend.

By the end of this tutorial, you will have a comprehensive understanding of the Delta Producer and Consumer stacks, empowering you to build dynamic and synchronized applications that efficiently handle data updates. So, let's dive in and embark on the journey of mastering delta synchronization!

# Step 0: Setup
The producer and consumer stacks are simple book application to showcase how the sync flow works between the producer and consumer stack.

## Step 0.1: Pull the Producer Stack
To begin, we'll pull the Producer stack, which includes the application for editing the list of books. Follow these steps:
1. Open your terminal or command prompt.
2. Navigate to the desired directory where you want to store the Producer stack.
3. Execute the following command to pull the Producer stack:
```bash
git clone https://github.com/Jan-PieterBaert/delta-tutorial-producer
```
4. Wait for the pull process to complete. You now have the latest version of the application used to manage the list of books.
## Step 0.2: Pull the Consumer Stack
Next, let's pull the Consumer stack, which comprises the application for viewing the books. Follow these steps:
1. Keep the terminal or command prompt open.
2. Navigate to the directory where you'd like to store the Consumer stack.
3. Use the following command to pull the Consumer stack:
```bash
git clone https://github.com/Jan-PieterBaert/delta-tutorial-consumer
```
4. Allow the process to finish. You now have the most up-to-date version of the application used to view the list of books.



# Step 1: introducing the producer flow
In this step, we will walk you through the process of adding various containers to set up the publication flow.
Each container is added in the `docker-compose.yml` file and plays a crucial role in managing different aspects of the publication process. Let's get started!

## Step 1.1: Adding the mu-authorization; the authentication layer above the Database
The mu-authorization layer ensures secure access control to the database. Follow these steps to add it:
1. Add the `mu-authorization` container with appropriate configurations to control access to the database.

## Step 1.2: Adding the Resource and Cache Containers for Job/Task Objects
The resource and cache containers optimize the handling of job/task objects. Follow these steps to add it:
1. Configure and add the `resource` container to efficiently manage job/task objects.
2. Add the `cache` container to store frequently accessed job/task data for faster retrieval.

## Step 1.3: Adding the Delta-Notifier Container
The delta-notifier informs other services about changes in the database. Follow these steps to add it:
1. Add the `delta-notifier` container with the necessary configuration to enable it to send notifications when changes occur.

## Step 1.4: Adding the File Service Container
The file service container is responsible for managing files. Follow these steps to add it:
1. Add the `file service` container with appropriate configurations to handle file-related operations.

## Step 1.5: Adding the Job-Controller and Scheduled-Job-Controller Containers
The job-controller and scheduled-job-controller containers handle the scheduling of tasks and jobs. Follow these steps to add them:
1. Configure and add the `job-controller` container to schedule tasks/jobs based on events or user actions.
2. Add the `scheduled-job-controller` container to manage recurring jobs or tasks on a predetermined schedule.

## Step 1.6: Adding the Delta-Producer-Dump-File-Publisher Container
The delta-producer-dump-file-publisher container publishes dump-files to the triplestore. Follow these steps to add it:
1. Add the `delta-producer-dump-file-publisher` container with the necessary configurations to publish the dump-files to the triplestore.

## Step 1.7: Adding the Delta-Producer-Publication-Graph-Maintainer Container
The delta-producer-publication-graph-maintainer ensures the publication graph remains consistent with the database content. Follow these steps to add it:
1. Configure and add the `delta-producer-publication-graph-maintainer` container, which optionally publishes differences as delta messages based on the specified configuration.

## Step 1.8: Adding the Delta-Producer-Background-Jobs-Initiator Container
The delta-producer-background-jobs-initiator creates background jobs, including the initial sync, periodic healing, and dumping jobs. Follow these steps to add it:
1. Add the `delta-producer-background-jobs-initiator` container, setting up the necessary configurations for creating and managing background jobs.

# Step 2: configuring the producer flow
In this step, we will walk you through the essential configuration steps for various publisher components. Each component plays a crucial role in managing the publication flow effectively. Let's get started!

## Step 2.1: Configuring mu-authorization
mu-authorization requires two crucial configurations:
1. `config.ex`: Define permissions for editing specific kinds of triples based on user roles or groups.
2. `delta.ex`: Configure delta notifications to ensure mu-authorization provides the delta-notifier with necessary delta messages.
With these configurations in place, the jobs and tasks can be created and the delta-notifier will work as usual.

## Step 2.2: Configuring the Resource
The resource configuration files, located at `config/resources/`, define the data model for tasks and jobs.
This allows the job services to create and consume the jobs.

## Step 2.3: Configuring the Delta-Notifier
In this step, we will focus on configuring the delta-notifier, a critical component that includes various services expecting delta messages. There are a few important aspects to consider during its configuration:

- The delta-producer-publication-graph-maintainer service is responsible for receiving delta messages from specific graphs. To set up this configuration, follow these steps:
	1. Specify the necessary graphs from which the maintainer should receive delta messages.
		- Ensure you have one configuration entry for each graph that needs to be monitored.
	2. Pay close attention when modifying the graph filters or including multiple graphs.
		- Removing the graph filter or including an excessive number of graphs may overwhelm the `delta-producer-publication-graph-maintainer` service.

Keep in mind that other entries may need to be added based on your application's specific requirements. Customization allows you to tailor the configuration to suit your project's functionality effectively.

## Step 2.4: Configuring the Job-Controller
In the job-controller configuration, specify tasks for a job in a specific order. Note that job URIs may change, so customize them accordingly and keep them synchronized across all configurations for consistency. Proper configuration ensures effective task sequencing and maintains a coherent publication flow.

## Step 2.5: Configuring the Delta-Producer-Dump-File-Publisher
Configure the `delta-producer-dump-file-publisher` with the following details:
- `fileBaseName`: Specify the location to find the dumps for the jobs.
- Keep the job URIs in sync across all configurations to ensure consistency.

## Step 2.6: Configuring the Delta-Producer-Publication-Graph-Maintainer
Configure the `delta-producer-publication-graph-maintainer` with the following details:
- Make the the job URIs are synced with other config files to maintain consistency.
- Verify that the graphs for errors, files and jobs match other configured services.
- Note that delta files are generated at the `relativeFilePath` when `serveDeltaFiles` is enabled.
- When the healing process takes too much time because the dateset is too large, it is advised to use the `useFileDiff` option for memory-efficient healing on big datasets.
- Enable `logOutgoingDelta` for debugging created deltas.
- Set up the export config to synchronize patterns to the publication graph, specified in the file specified by `exportConfigPath`.
   - For each type, define a list of graphs and properties that should be synchronized.
   - Ensure the graph is the same as the graph your application creates triples in.

## Step 2.7: Configuring the Delta-Producer-Background-Jobs-Initiator
Configure the `delta-producer-background-jobs-initiator` with the following details:
- Ensure `jobsGraph` is the same across all configurations.
- Make the the job URIs are synced with other config files to maintain consistency.
- Enable `startInitialSync` if initial sync has never been executed. This ensures it gets executed, as healing jobs can only start after the initial sync has run.
- Use cronjob patterns to determine when new jobs should be created for dump and healing tasks.
    - For more information about cronjobs, refer to [https://crontab.guru/](https://crontab.guru/) (note: the cronjob pattern here includes a `second` option before all others).

With these configurations, you can effectively initiate background jobs and control their scheduling for smooth operation of your publication flow.

## Step 2.8: Checking Job URIs and Graph Sync Across Config Files
To ensure proper synchronization and consistent data management, you need to check that specific job URIs and graphs are in sync across the relevant configuration files. Here's a breakdown of the elements you need to verify:

### Job URIs to Keep in Sync:
1. `dcatDataSetSubject`
    - In `config/delta-producer/dump-file-publisher/config.json`, verify this URI for each key.
2. `deltaDumpFileCreation`
    - In `config/delta-producer/dump-file-publisher/config.json`, ensure it is used as the key for the dump configuration.
    - In `config/delta-producer/background-job-initiator/config.json`, confirm it is used as `dumpFileCreationJobOperation`.
    - In `config/job-controller/config.json`, validate it as a key for its task configuration.
3. `initialPublicationGraphSyncing`
    - In `config/delta-producer/dump-file-publisher/config.json`, ensure it is used as the key for the dump configuration.
    - In `config/delta-producer/publication-graph-maintainer/config.json`, verify it is used as `initialPublicationGraphSyncJobOperation`.
    - In `config/job-controller/config.json`, validate it as a key for its task configuration.
4. `healingJobOperation`
    - In `config/job-controller/config.json`, confirm it is used as a key for its task configuration.
    - In `config/delta-producer/publication-graph-maintainer/config.json`, verify it as `healingJobOperation`.
    - In `config/delta-producer/background-job-initiator/config.json`, ensure it is used as `healingJobOperation`.

### Graphs to Keep in Sync:
1. `jobsGraph`
    - In `config/delta-producer/background-job-initiator/config.json`, verify its presence.
    - In `config/delta-producer/publication-graph-maintainer/config.json`, ensure it is defined correctly.
    - In `config/authorization/config.ex`, check the part for jobs to find the `%GraphSpec` where it should be the graph.
    - In `config/delta/rules.js`, confirm it is used as a graph filter for messages sent to the `delta-producer-publication-graph-maintainer` service.
2. `publicationGraph`
    - In `config/delta-producer/publication-graph-maintainer/config.json`, verify its definition.
    - In `config/delta-producer/dump-file-publisher/config.json`, confirm its use as `targetGraph`.
3. `filesGraph`
    - In the file service section of `docker-compose.yml`, ensure its presence.
    - In `config/delta-producer/publication-graph-maintainer/config.json`, verify its usage.

By thoroughly verifying these job URIs and graphs across different configuration files, you can maintain synchronization and facilitate a seamless publication flow.

## Step 2.9: Checking the Output
After configuring the delta producer and related components, it's essential to verify the output:
1. Ensure the initial dump exists at the configured location (example application: `data/files/delta-producer-dumps/initial-dump-books/`).
2. Make a change in the application and wait for the healing process to execute.
3. Check that the delta exists at the configured location (example application: `data/files/deltas/books/$(date +%Y-%m-%d)/`).

By following these steps, you can ensure the delta producer is functioning correctly, and deltas are generated and stored as expected.

# Step 3: introducing the consumer flow
## Step 3.1: Adding the mu-authorization; the authentication layer above the Database
The mu-authorization layer ensures secure access control to the database. Follow these steps to add it:
1. Add the `mu-authorization` container with appropriate configurations to control access to the database.

## Step 3.2: Adding the Delta-Consumer Service
The `delta-consumer` service is responsible for consuming both the initial dump and delta messages from the `delta-producer`.
By incorporating this service, your application can efficiently process and utilize the data updates generated by the producer side.

# Step 4: introducing the consumer flow
## Step 4.1: Configuring mu-authorization
For this part we refer back to step 2.1, since the mu-authorization configuration is similar.

## Step 4.2: Consumer Configuration Variables to Consider
When configuring the consumer, pay attention to the following variables:
1. `DCR_SYNC_BASE_URL`: Set to `http://machine-ip:8888` (avoid using `localhost` as it resolves to the container itself).
2. `DCR_SYNC_FILES_PATH`: Define the path of the sync files, configured in the dispatcher of the producer.
3. `DCR_SYNC_DATASET_SUBJECT`: Specify the `dcatDataSetSubject` job URI configured on the producer side.
4. `DCR_DISABLE_DELTA_INGEST` and `DCR_DISABLE_INITIAL_SYNC`: In the `docker-compose.yml` file, set these variables to `true` and use an overriding docker-compose configuration file to switch them to `false` when needed.
5. `INGEST_GRAPH`: The default is `http://mu.semte.ch/graphs/public`, but some backend services might require different graphs. Adjust accordingly based on your backend requirements.

> **Note**
> In order to start syncing, the consumer needs to do an initial sync and delta ingest. You'll have to run the override once where `DCR_DISABLE_DELTA_INGEST` and `DCR_DISABLE_INITIAL_SYNC` are set to false. After this you can run the normal docker compose with the values set to true again.

By correctly configuring these variables, you can ensure smooth operation and data processing within the consumer component of your application.

## Step 4.3: Checking the Output
After setting up the consumer and ingesting the initial dump and delta files, it's crucial to verify that the data is correctly synchronized between the producer and consumer. You can do this in two ways:
1. **Using the Books Application:** Edit the books list on the producer side at `http://localhost:8888/books-frontend/book`, and wait until the sync, including healing that creates deltas and ingestion on the consumer side, is completed. Then, check that the list is the same on the consumer side at `http://localhost:8877/books-frontend/book`.
2. **Comparing SPARQL Query Outputs:** Compare the SPARQL query outputs on `localhost:8879/sparql` (consumer) and `localhost:8889/sparql` (producer). Ensure that the data retrieved from both endpoints is consistent and reflects the changes made on the producer side.

By performing these checks, you can ensure that the data synchronization is working correctly, and the producer and consumer are in harmony.

# Step 5: Implementing Producer-Side Authentication
In this final step, we will add authentication to the producer side. Follow these steps to secure the producer:
1. **Generate a Key**: Create a unique key, which can be any string, to use for authentication.
2. **Add the Key to `publication-graph-maintainer` Configuration**: In the `config/delta-producer/publication-graph-maintainer/config.json` file, include the generated key for the correct service.
3. **Update Dispatcher Configuration**: On the producer side, update the dispatcher configuration to include a login route for the correct service. This ensures secure authentication.
4. **Add the Key to the Consumer Configuration**: On the consumer side, add the generated key in the `docker-compose.yml` file for the correct consumer service.
5. **Include the Login URL for the Consumer Service**: Also on the consumer side, add the full login URL in the `docker-compose.yml` file for the correct consumer service. This connects the consumer with the authentication process.

By implementing these authentication measures on the producer side, you enhance the security and integrity of your data publication and consumption processes.

# Step 6: Producer-Side file sharing
In this final step we show where the delta files can be found.
Since the `delta-producer-publication-graph-maintainer` service makes these delta files and they can be accessed at the configured endpoint (and forwarded by the dispatcher)
