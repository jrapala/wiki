# Circuitry

A **logic gate** is an electronic component that can be used to conduct electricity based on a rule. The output of the gate is the result of applying this rule to one or more "inputs". These inputs may be two wires or the output of other logic gates.

Logic gates are digital components. They normally work at only two levels of voltage, a positive level and zero level. Commonly they work based on two states: On and Off. In the On state, voltage is positive. In the Off state, the voltage is at zero. The On state usually uses a voltage in the range of 3.5 to 5 volts. This range can be lower for some uses.

Logic gates compare the state at their inputs to decide what the state at their output should be. A logic gate is on or active when its rules are correctly met. At this time, electricity is flowing through the gate and the voltage at its output is at the level of its On state.

Logic gates are electronic versions of Boolean logic. Truth tables will tell you what the output will be, depending on the inputs.



**OR Logic Gate**

[![img](https://upload.wikimedia.org/wikipedia/commons/thumb/4/4c/Or-gate-en.svg/200px-Or-gate-en.svg.png)](https://simple.wikipedia.org/wiki/File:Or-gate-en.svg)

OR gates have two inputs. The output of an OR gate will be on if at least one of the inputs are on. If both inputs are off, the output will be off.

Using the image, if either *A* **or** *B* is On, the output (*out*) will also be On. If both *A* and *B* are Off, the output will be Off.

|  A   |  B   | Output |
| :--: | :--: | :----: |
| Off  | Off  |  Off   |
|  On  | Off  |   On   |
| Off  |  On  |   On   |
|  On  |  On  |   On   |



**NOT Logic Gate**

[![img](https://upload.wikimedia.org/wikipedia/commons/thumb/9/9f/Not-gate-en.svg/200px-Not-gate-en.svg.png)](https://simple.wikipedia.org/wiki/File:Not-gate-en.svg)

The NOT logic gate has only one input. If the input is On then the output will be Off. In other words, the NOT logic gate changes the signal from On to Off or from Off to On. It is sometimes called an inverter.

|  A   | Output |
| :--: | :----: |
| Off  |   On   |
|  On  |  Off   |





**XOR Logic Gate**

[![img](https://upload.wikimedia.org/wikipedia/commons/thumb/6/6d/Xor-gate-en.svg/220px-Xor-gate-en.svg.png)](https://simple.wikipedia.org/wiki/File:Xor-gate-en.svg)

XOR gates have two inputs. The output of a XOR gate will be true only if the two inputs are different from each other. If both inputs are the same, the output will be off.

|  A   |  B   | Output |
| :--: | :--: | :----: |
|  On  |  On  |  Off   |
|  On  | Off  |   On   |
| Off  |  On  |   On   |
| Off  | Off  |        |



**NAND Logic Gate**

[![img](https://upload.wikimedia.org/wikipedia/commons/thumb/b/be/Logic-gate-nand-de.png/220px-Logic-gate-nand-de.png)](https://simple.wikipedia.org/wiki/File:Logic-gate-nand-de.png)

NAND means not both. It is called NAND because it means "not and." This means that it will always output true unless both inputs are on.

|  A   |  B   | Output |
| :--: | :--: | :----: |
|  On  |  On  |  Off   |
|  On  | Off  |   On   |
| Off  |  On  |   On   |
| Off  | Off  |   On   |



**XNOR Logic Gate**

[![img](https://upload.wikimedia.org/wikipedia/commons/thumb/9/9b/Logic-gate-xnor-us.png/220px-Logic-gate-xnor-us.png)](https://simple.wikipedia.org/wiki/File:Logic-gate-xnor-us.png)

XNOR means "not or." This means that it will only output true if both inputs are the same. It is the opposite of a XOR logic gate.

|  A   |  B   | Output |
| :--: | :--: | :----: |
|  On  |  On  |   On   |
|  On  | Off  |  Off   |
| Off  |  On  |  Off   |
| Off  | Off  |   On   |