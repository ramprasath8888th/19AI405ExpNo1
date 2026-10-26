<h1>ExpNo 1 :Developing AI Agent with PEAS Description</h1>
<h3>Name: V DHIVYA </h3>
<h3>Register Number: 212225100011</h3>


<h3>AIM:</h3>
<br>
<p>To find the PEAS description for the given AI problem and develop an AI agent.</p>
<br>
<h3>Theory</h3>
<h3>Medicine prescribing agent:</h3>
<p>Such this agent prescribes medicine for fever (greater than 98.5 degrees) which we consider here as unhealthy, by the user temperature input, and another environment is rooms in the hospital (two rooms). This agent has to consider two factors one is room location and an unhealthy patient in a random room, the agent has to move from one room to another to check and treat the unhealthy person. The performance of the agent is calculated by incrementing performance and each time after treating in one room again it has to check another room so that the movement causes the agent to reduce its performance. Hence, agents prescribe medicine to unhealthy.</p>
<hr>
<h3>PEAS DESCRIPTION:</h3>
<table>
  <tr>
    <td><strong>Agent Type</strong></td>
    <td><strong>Performance</strong></td>
     <td><strong>Environment</strong></td>
    <td><strong>Actuators</strong></td>
    <td><strong>Sensors</strong></td>
  </tr>
    <tr>
    <td><strong>Medicine prescribing agent</strong></td>
    <td><strong>Treating unhealthy, agent movement</strong></td>
     <td><strong>Rooms, Patient</strong></td>
    <td><strong>Medicine, Treatment</strong></td>
    <td><strong>Location, Temperature of patient</strong></td>
  </tr>
</table>
<hr>
<H3>DESIGN STEPS</H3>
<h3>STEP 1:Identifying the input:</h3>
<p>Temperature from patients, Location.</p>
<h3>STEP 2:Identifying the output:</h3>
<p>Prescribe medicine if the patient in a random has a fever.</p>
<h3>STEP 3:Developing the PEAS description:</h3>
<p>PEAS description is developed by the performance, environment, actuators, and sensors in an agent.</p>
<h3>STEP 4:Implementing the AI agent:</h3>
<p>Treat unhealthy patients in each room. And check for the unhealthy patients in random room</p>
<h3>STEP 5:</h3>
<p>Measure the performance parameters: For each treatment performance incremented, for each movement performance decremented</p>
#PROGRAM 
```
class Thing:  
    """ 
    This represents any physical object that can appear in an Environment. """ 
 
    def is_alive(self): 
        """Things that are 'alive' should return true.""" 
        return hasattr(self, "alive") and self.alive 
 
    def show_state(self): 
        """Display the agent's internal state. Subclasses should override.""" 
        print("I don't know how to show_state.") 
 
 
# In[9]: 
 
 
class Agent(Thing): 
     
    """ 
        An Agent is a subclass of Thing """ 
 
    def __init__(self, program=None): 
        self.alive = True 
        self.performance = 0  
        self.program = program 
 
    def can_grab(self, thing): 
        """Return True if this agent can grab this thing. Override for appropriate subclasses 
of Agent and Thing."""  
        return False 
 
 
# In[10]: 
 
 
def TableDrivenAgentProgram(table):  
    """ 
    [Figure 2.7] 
    This agent selects an action based on the percept sequence. It is practical only for tiny 
domains. 
    To customize it, provide as table a dictionary of all 
    {percept_sequence:action} pairs. """ 
    percepts = [] 
     
 
    def program(percept): 
        action = None 
        percepts.append(percept) 
        action = table.get(tuple(percepts)) 
        return action  
    return program 
 
 
# In[11]: 
 
 
room_A, room_B = (0,0), (1,0) # The two locations for the Doctor to treat 
 
 
# In[12]: 
 
 
def TableDrivenVacuumCleanerAgent(): 
    """ 
    Tabular approach towards hospital function.  
    """ 
         
    table = { 
    ((room_A, "clean"),): "Right", 
    ((room_A, "dirty"),): "suck", 
    ((room_B, "clean"),): "Left", 
    ((room_B, "dirty"),): "suck", 
    ((room_A, "dirty"), (room_A, "clean")): "Right", 
    ((room_A, "clean"), (room_B, "dirty")): "suck", 
    ((room_B, "clean"), (room_A, "dirty")): "suck", 
    ((room_B, "dirty"), (room_B, "clean")): "Left", 
    ((room_A, "dirty"), (room_A, "clean"), (room_B, "dirty")): "suck", 
    ((room_B, "dirty"), (room_B, "clean"), (room_A, "dirty")): "suck", 
    } 
    return Agent(TableDrivenAgentProgram(table)) 
 
 
# In[13]: 
 
 
TableDrivenVacuumCleanerAgent() 
 
 
# In[ ]: 
 
 
 
 
 
# In[14]: 
 
 
class Environment: 
    """Abstract class representing an Environment. 'Real' Environment classes inherit from 
this. Your Environment will typically need to implement: 
    percept: Define the percept that an agent sees. execute_action: Define the 
effects of executing an action. 
    Also update the agent.performance slot. 
    The environment keeps a list of .things and .agents (which is a subset of .things). Each 
agent has a .performance slot, initialized to 0. 
    Each thing has a .location slot, even though some environments may not need this.""" 
 
    def __init__(self): 
        self.things = []  
        self.agents = [] 
        #room_A, room_B = (0,0), (1,0) # The two locations for the Doctor to treat 
 
    def percept(self, agent): 
        """Return the percept that the agent sees at this point. (Implement this.)""" 
        raise NotImplementedError 
 
    def execute_action(self, agent, action): 
        """Change the world to reflect this action. (Implement this.)"""  
        raise NotImplementedError 
 
    def default_location(self, thing): 
        """Default location to place a new thing with unspecified location.""" 
        return None 
 
    def is_done(self): 
        """By default, we're done when we can't find a live agent."""  
        return not any(agent.is_alive() for agent in self.agents) 
 
    def step(self): 
        """Run the environment for one time step. If the 
        actions and exogenous changes are independent, this method will do. If there are 
interactions between them, you'll need to override this method.""" 
        if not self.is_done():  
            actions = [] 
            for agent in self.agents: 
                if agent.alive: 
                    actions.append(agent.program(self.percept(agent)))  
                else: 
                    actions.append("") 
            for (agent, action) in zip(self.agents, actions):  
                self.execute_action(agent, action) 
 
    def run(self, steps=1000): 
        """Run the Environment for given number of time steps.""" 
        for step in range(steps): 
            if self.is_done(): 
                return  
            self.step() 
 
    def add_thing(self, thing, location=None): 
        """Add a thing to the environment, setting its location. For convenience, if thing is 
an agent program we make a new agent for it. (Shouldn't need to override this.)""" 
        if not isinstance(thing, Thing): 
            thing = Agent(thing) 
        if thing in self.things: 
            print("Can't add the same thing twice")  
        else: 
            thing.location = (location if location is not None else self.default_location(thing)) 
            self.things.append(thing)  
            if isinstance(thing, Agent): 
                thing.performance = 0  
                self.agents.append(thing) 
 
    def delete_thing(self, thing): 
        """Remove a thing from the environment.""" 
        try: 
             
            self.things.remove(thing)  
        except ValueError as e: 
            print(e) 
            print(" in Environment delete_thing") 
            print(" Thing to be removed: {} at {}".format(thing, thing.location)) 
            print(" from list: {}".format([(thing, thing.location) for thing in self.things])) 
        if thing in self.agents:  
            self.agents.remove(thing) 
 
 
# In[15]: 
 
 
class TrivialIndoorEnvironment(Environment): 
    """This environment has two locations, A and B. Each can be unhealthy or healthy. The 
agent perceives its location and the location's status. This serves as an example of how 
to implement a simple Environment.""" 
 
    def __init__(self): 
        super().__init__() 
        #room_A, room_B = (0,0), (1,0) # The two locations for the Doctor to treat 
        self.status = {room_A: "dirty", room_B: "clean"} 
 
    def thing_classes(self): 
        return [TableDrivenDocterAgent] 
 
    def percept(self, agent): 
        """Returns the agent's location, and the location status (unhealthy/healthy).""" 
        return agent.location, self.status[agent.location] 
 
    def execute_action(self, agent, action): 
        """Change agent's location and/or location's status; track performance. Score 10 for 
each treatment; -1 for each move.""" 
        if action == "Right": 
            agent.location = room_B 
            agent.performance -= 1 
        elif action == "Left": 
            agent.location = room_A 
            agent.performance -= 1 
        elif action == "suck": 
            #tem=float(input("Enter your temperature"))  
            #if tem>=98.5: 
            self.status[agent.location] == "dirty" 
                #print("medicine prescribed: paracetamol and anti-biotic(low dose)") 
            agent.performance += 10 
        self.status[agent.location] = "clean" 
 
 
    def default_location(self, thing): 
            
        return room_A 
 
 
# In[16]: 
 
 
if   __name__ == "__main__": 
     
    agent = TableDrivenVacuumCleanerAgent() 
    environment = TrivialIndoorEnvironment() 
    #print(environment) 
    environment.add_thing(agent) 
    print("\tStatus of Vacuum Cleaner before cleaning") 
    print(environment.status) 
    print("AgentLocation : {0}".format(agent.location))  
    print("Performance : {0}".format(agent.performance)) 
    for i in range(2): 
        environment.run(steps=1) 
        print("\n\tStatus of Vacuum Cleaner after cleaning")  
        print(environment.status) 
        print("AgentLocation : {0}".format(agent.location))  
        print("Performance : {0}".format(agent.performance)) 
```
#OUTPUT
<img width="632" height="312" alt="image" src="https://github.com/user-attachments/assets/0d85f870-1569-433c-b93f-1b674ecc9693" />
#RESULT
Thus the developing AI Agent with PEAS Description was implemented using Python Programming Successfully.
