# Bitcoin Mining Network using ATmega32
A simplified and scalable Bitcoin mining network simulation using the 
AVR-ATMEGA32 microcontroller unit (MCU).

This project signifies the culmination of my work on AVR-ATMEGA32 MCU 
interfacing. I have developed an application emulating the concept of Bitcoin 
mining.

The primary goal of this project was to try to use most of the devices and 
peripherals that I have interfaced along with studying how crypto-currencies 
work.

![img](output.gif)

# Architecture
This project consists of 3 types of MCUs:
- Generator: Emulates the Bitcoin network's mempool. It's responsible for 
generating random transactions and storing them into a block, along with the 
block header containing the previous block hash and nonce. Only one generator is 
needed.
- Miner: Emulates the Bitcoin miner. It receives the block and header from the 
generator and starts mining for the nonce that will make the block hash start 
with a certain number of leading zeros (the target). Up to 8 miners using the 
same code can connect over I2C.
- Manager: Detects miners and assigns each a unique ID. It also sets the nonce 
search space for each miner before mining starts. Only one manager is needed.

Each MCU has its own LCD that writes progress messages and error codes.

# Features
- Scalability: any number of miners can be added, since they all 
use the same source code. The manager can detect up to 8 miners currently, due to the 
3-to-8 decoder used. This can be expanded by using a larger decoder.

- Simplicity: simplifies mining by not using the Bitcoin mining real 
cryptographic algorithms(SHA256). Instead, it uses the FNV-1a algorithm to 
calculate a 4-byte block hash and compare it to the target. This is because of 
the limited computational ability of the ATmega32.

# Execution
The process starts by each MCU initializes their peripherals, then:
- Manager: detects miners in the network as long as the MINER_REQUEST signal is 
Low. This is initially done to count the number of miners currently in the 
network. Then the manger splits the range of the nonce over the number of 
miners. Then, the ranges is sent to each miner.
- Miners: they wait to receive ACK from the manger in the form of an ID sent 
twice. Then, the miner replies by the ID received. The manager checks that is 
got back what it sent, increment a counter, and moves on to check if there are 
other miners in the network. Each miner then waits to receive its range which is 
checked.
- Generator: generates random transactions by reading random values converted by 
the ADC. This process replicates the process of selecting transactions from the 
mempool in real bitcoin mining where these transactions are received and stored 
locally for each mining cluster. Then, the generator sets the target for the 
hash, which is the constraint where the resultant hash of the block, after 
trying different nonce, has a number of zeros at the begining the hash. After 
the block is prepared, the generator waits for a signal from the manager to 
start broadcasting the block to all miners

When a miner finds the correct nonce, it sends it to the generator to verify it 
and then write the resultant hash to the EEPROM which emulates the Blockchain. 
The real Blockchain is contained in a linked list but due to the limitation on 
memory in embedded systems, a circular linked list is used in the EEPROM.


#AVR #ATMEGA32 #BitcoinMining #MCU #EmbeddedSystems #Innovation #OpenSource


