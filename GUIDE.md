#### *Command options:*
 - *To specify the begin state one can use: ***'-b'*** ***'--begin-state'*** (Default: 'Q0');*
 - *To specify the end state one can use: ***'-e'*** ***'--end-state'*** (Default: 'QDONE');*
 - *To show a heads-up-display use: ***'-d'*** ***'--display'*** (Default: 'False'):*
    - *If you wish to specify the rendering speed (in seconds) use: ***'-s'*** ***'--speed'*** (Default: '0.01').*
 - *To input the configuration file (also, known as the instructions) use: ***'-c'*** ***'--config'*** (Required);*
    > ***The config file:***
    > *The machine reads a JSON file as instruction and processes the input accordingly.*
    > 
    > ***But how does it work?***
    > - *The file is read and converted into a dictionary containing all possible states and instructions. It is then validated and an exception is thrown, should the definition contain any errors (for example, if a state is referenced but never defined). Throughout the process the dictionary is used ad-hoc to find out:*
    >   - *What should be written on the tape (can be any character)?*
    >   - *Which direction the tape should move (right or left)?*
    >   - *What state the machine should switch to (any state as defined before)?*
    > 
    > *However, one should know that the file is structured and subjected to some constraints, as follows:*
    > ```jsonc
    > {
    >     "<STATE-NAME>": { // THE STATE-NAME CAN BE ANYTHING.
    >         "<CHARACTER-ACTION>": { // WHICH IS ONLY DEFINED IF THE TURING MACHINE READS THIS CHARACTER.
    >             "WRITE": "<CHARACTER-TO-WRITE>", // IT IS THE ONE CHARACTER TO WRITE AT THE CURRENT INDEX.
    >             "MOVE": "<RIGHT|LEFT>", // THE DIRECTION IN WHICH THE TAPE SHOULD MOVE: 'LEFT' OR RIGHT' (IT NEEDS TO BE UPPERCASE).
    >             "NEXTSTATE": "<NEXT-STATE-NAME>" // THE NAME OF THE STATE TO USE NEXT (OBVIOUSLY, THAT STATE ALSO NEEDS TO BE DEFINED, OTHERWISE AN EXCEPTION WILL BE THROWN).
    >         }
    >     }
    > }
    > ```

> [!TIP]
> *Your machine should not move more to the left then the initial input was. Otherwise you will overwrite your own configuration, which is not optimal.*
>    - *This could be fixed by changing the configuration of `instructions/universal.json` to shift the output to the right if you get too close to the configuration;*
>    - *Another possible fix would be to add a second tape, which only contains the input. This is a very common way to implement the universal Turing machine as it allows for more speed.*

 - *To specify the input tape use: ***'-t'*** ***'--tape'*** (Required).*
   > *The input is designed to be as simple as possible. And, therefore, is bound by the certain rules.* ***So, what to put on the tape?***
   > 1. *We assume you have named all your states with integers >=1.*
   > 2. *You need to convert all values to the following:*
   >     - *`QX -> 0{X}`*
   >     - *`'0' -> 0`*
   >     - *`'1' -> 00`*
   >     - *`' ' -> 000`*
   >     - *`RIGHT -> 00`*
   >     - *`LEFT -> 0`*
   > 3. *You also need to put the converted values together in the following order, and use `1` as separator for the actual state, symbol on the tape, new state, symbol to write on the tape, and direction to move.*
   > 4. *Concat all converted states using `11` as separator;*
   > 5. *Add `111` to the end of the configuration;*
   > 6. *Add your custom input at the end.*

> [!IMPORTANT]
> *If you use `Q0` as a valid state, you need to change from `QX -> 0{X}` to `QX -> 0{X+1}` otherwise the machine will not run.*

> *In the example below, we're using only the required inputs, in addition to the `--display` option, which activates the heads-up-display, and the `--speed` option, which provides (in this case) a slower override to the default rendering speed.*
> ```bash
> # MULTIPLICATION: 21 * 3
> python3 ./universal_turing_machine.py --config instructions/multiplication.json \
>     --tape "000000000000000000000 000" \
>     --display
> ```
> *In this more complicated example, alongside the required inputs and the `--display` and `--speed` options (which purpose is the same as in the previous example), we're also setting a begin and end state for the machine, respectively, by using the `--begin-state` and `--end-state` options.*
> ```bash
> # MULTIPLICATION FOR A UNIVERSAL CONFIGURATION
> python3 ./universal_turing_machine.py --config instructions/universal.json \
>      --tape "010001001000100110101000100010011001000100000000000000010001001100101000100010011000100010000100010011000101000101001100001000100000000000000001000101100001010000010001001100000100010000001000100110000010100000101001100000010001000000010101100000010100000010100110000000100010000000010001011000000010100000001010110000000010001000000000010101100000000101000000000101011000000000100010000101001100000000010100000000010101100000000001000100000000000100010110000000000101000000000010101100000000000100010000000000000100010011000000000001010000000000001010110000000000001000101000100110000000000001010000000000001010110000000000000100010000000000000100010011000000000000010100000000000000100010011000000000000001010000000000000010001001100000000000000010100000000000000010001001100000000000000001000100000000000000000100010110000000000000000101000000000000000010001011000000000000000001010000000000000000010001011100 00" \
>      --begin-state Q1 \
>      --end-state Q82 \
>      --display
> ```
