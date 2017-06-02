## What is Needed

A typical enterprise Data Center (DC) is driven by the [ITSM Change Management](https://en.wikipedia.org/wiki/Change_management_(ITSM)) with a careful planning of the infrastructure. An overview to get the idea how typically services are built at Architecture and Change Management and how process looks like, can be found on [Open Group Portal (PDF)](http://www.opengroup.org/architecture/0404brus/presents/rajesh/aandc1.pdf).

Constantly growing number of managed machines will eventually grow up even more. Therefore Salt is required to handle a vast amount of machines, while meet the requirements for a typical DC run.

- **Change the configuration on the machines within a certain period of time.**
  
  This is a typical maintenance window, where N amount of machines needs to be updated per a specific time and so such operation _must_ be finished within its boundaries.

- **Handle the resulting data of managed machines in the way so the other 3rd party systems can be equally integrated**

  In this case Salt needs to organise the data in the way that any 3rd party application should be able to cope with that amount of obtained data.

- **Allow horizontal scalability into a grid/cluster**

  Horizontal scalability allows to add more machine nodes that aren't necessary very powerful, as it is done in vertical scalability by adding more hardware. Grid layout would allow to certain vital points stay available after the impacts.

- **Be 3rd party app agnostic in doing previous requirements**

  Salt should not rely on a specific 3rd party application that becomes a direct dependency in order to perform data organising and handling, so it allows to build an infrastructures with desired components.

## Full Process Data Limitations

By "fully processed" it means that three kind of information data state went through the full circle. That is:

1. Requesting information (what a Minion should do) has been sent from Master to Minion
2. Returning meta-data (_about_ what Minion has been done) has been sent from Minion to Master
3. Returned data (result of Minion job) has been sent from Minion to Master

With only one Master, Salt hits the limits of amount of machines that can be fully processed within a certain amount of time. So even if Salt Manager will fully process an N amount of minions, it still won't make it to fully process it within _certain period of time_ and therefore won't meet the requirements for the ITSM Change Management.

## Conceptual Architecture


## Opened Questions
