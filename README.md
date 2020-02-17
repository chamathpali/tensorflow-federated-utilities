# 🛠 TensorFlow Federated Utilities 🛠
Aim is to create and maintain helper methods to support TFF functionalities which are generally required to support the upcoming federated setting. Ex: Data model conversions...

Helper methods overview.

1. [Client Simulation dataset for each clients](#create_fed_data) 
2. More to come....

## create_fed_data
This method can be used to build the simulation dataset in the TF dataset format **[(tf.data.Dataset)](https://www.tensorflow.org/api_docs/python/tf/data/Dataset)** which is required to build up the federated settings. And the end result is to build the full dataset into a **[tff.simulation.ClientData](https://www.tensorflow.org/federated/api_docs/python/tff/simulation/ClientData)** type.

### Usage
1. Using the tff.simulation.ClientData.from_clients_and_fn which allows to pass a function to build the dataset.

```python
CLIENTS = ['01', '02'];

train_data = tff.simulation.ClientData.from_clients_and_fn(
    client_ids=CLIENTS, 
    create_tf_dataset_for_client_fn=create_fed_data
)
````

2. Second step is to build the function to convert your existing data set into a TFF compatabile datatype.
The easiest way to visualize this is like the following format.

CLIENT_1 => (features, labels)

CLIENT_2 => (features, labels) ... 

```python
all_features = DATA #{dictionary in the above format} 

def create_fed_data(client_id):
    # Here we get only the clients 
    temp_client_data = all_features.get(clien_id)
    # You can have your own class to flatten the temporary data object into features and labels
    train_features, train_labels = flatten(temp_client_data)
    # Here we use the .from_tensor_slices to build up the dataset format required by tff
    dataset = tf.data.Dataset.from_tensor_slices(
        collections.OrderedDict({"x": train_features,"y": train_labels}))
    # Return the dataset for a single client_id
    return dataset
```

#### Sample Usecase
[Anjana W](https://github.com/anjanaw)'s [MEx](https://github.com/anjanaw/MEx/) Human activity recognition dataset was converted into TFF simulation dataset type using the above method.
