# Architecture
 
 ![architecture image](images/architecture.svg)
 
 * Utilitartian is decentralized. 
   *  async workers
   *  separate servies for receiving data
   
 * Multi utility.
 * Each component is independently horizontally scalable
 * Single point of failure in database and broker, but can be mitigated.
 * async worker for initiating pull operations.
 * separate receivng servers for push messages from meters
 
 * all data goes through the broker, easy to hook into the data flow to extend funtionallity and integrations to other systems
    * All message payloads are documented and open.
 
 * aims to enable the user to build their own integrations and subsystems. 
    * Open source frameworks
 
 * Should be possible to only run services you need.
 * Should be possible to only scale the services that needs scaling.
 * Gives you lots of freedom in how to run and operate the system.
 