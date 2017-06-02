## What is Needed

Constantly growing number of managed machines will eventually grow up even more. Therefore Salt is required to handle a vast amount of machines and be able to scale horizontally into a cluster. 

- **Change the configuration on the machines within a certain period of time.**
  
  This is a typical maintenance window, where N amount of machines needs to be updated per a specific time and so such operation _must_ be finished within its boundaries.

- **Handle the resulting data of managed machines in the way so the other 3rd party systems can be equally integrated**

  In this case Salt needs to organise the data in the way that any 3rd party application should be able to cope with that amount of obtained data.

- **Allow horizontal scalability**

  Horizontal scalability allows to add more machine nodes that aren't necessary very powerful, as it is done in vertical scalability by adding more hardware.

- **Be 3rd party app agnostic in doing previous requirements**

  Salt should not rely on a specific 3rd party application that becomes a direct dependency in order to perform data organising and handling, so it allows to build an infrastructures with desired components.

## Current Limitations

With only one Master, Salt is limited into a fixed amount of machines that can be fully processed within a certain amount of time. By "fully processed" it means that three kind of data went through the full circle. That is:

1. Requesting data (what a Minion should do)
2. Returning meta-data (_about_ what Minion has been done)
3. Returned data (result of Minion job)

## Conceptual Architecture


## Opened Questions
