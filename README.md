Documentation: dumpProcStat Bash Script
  The dumpProcStat script is a Bash script designed to retrieve and display process statistics on one or multiple hosts. It allows monitoring the memory usage, runtime, and other details of a specific process on different machines.

Usage
  To use the dumpProcStat script, execute it with the following command:
______________________________________________
./dumpProcStat <process_name> [<period>] [<hostfile>]
______________________________________________

Where:
  <process_name>: The name of the process for which you want to retrieve statistics.
  <period> (optional): The period (in seconds) at which the script retrieves process statistics. If not specified, the default is 0, which means the script retrieves the statistics once and exits.
  <hostfile> (optional): The path to a file containing a list of hosts (one per line) on which the process statistics should be retrieved. If not specified, the script retrieves statistics only on the local machine.

Functions
The script contains two main functions:

1. get_process_stats()
This function retrieves the process statistics for a single host. It takes the following parameters:
  process_name: The name of the process for which statistics should be retrieved.
The function performs the following steps:
Uses pgrep to find the process IDs (process_IDs) of all processes matching the given process_name.
Iterates over each process_ID and performs the following actions:
  Retrieves the command name (command_name) associated with the process ID using ps.
  Retrieves the current timestamp (timestamp) using date.
  Retrieves the memory usage (memory_usage) of the process using ps.
  Retrieves the runtime (runtime) of the process using ps.
  Prints the collected process statistics in the format: command_name HOSTNAME timestamp memory_usage runtime.

2. iterate_hosts()
This function iterates over a list of hosts and retrieves the process statistics for each host. It takes the following parameters:
  process_name: The name of the process for which statistics should be retrieved.
  period: The period (in seconds) at which the script retrieves process statistics.
  hostfile: The path to a file containing a list of hosts on which the process statistics should be retrieved.

The function performs the following steps:

Reads the hostfile and stores the hosts in an array.
Iterates over each host in the array and performs the following actions:
  Executes the get_process_stats function remotely on the host using ssh.
Calculates the time taken for a complete iteration (cycle_time) and sleeps for the remaining time until the next iteration.


Main Script Logic

The main script logic starts after the function definitions.

Checks if the number of command-line arguments ($#) is less than 1. If so, it displays a usage message and exits.
Sets the values of process_name, period, and hostfile based on the command-line arguments.
Prints a header line indicating the columns of the process statistics table: PROC_NAME HOST_NAME TIME MEMORY RUNTIME.
Determines the command ($cmd) to be executed based on the presence of a hostfile.
  If a hostfile is provided, it sets the command to iterate_hosts with the appropriate arguments.
  If no hostfile is provided, it sets the command to get_process_stats.
Enters an infinite loop using for (( ; ; )).
  Executes the $cmd to retrieve and display the process statistics.
  Checks for user input (q) every 0.1 seconds using read -t 0.1 -n 1 input.
    If the user enters q, the loop breaks and the script terminates.


Example Usage
Retrieve process statistics for a process named "my_process" once:
______________________________________________
./dumpProcStat my_process
______________________________________________

Retrieve process statistics for a process named "my_process" every 5 seconds:
______________________________________________
./dumpProcStat my_process 5
______________________________________________

Retrieve process statistics for a process named "my_process" on multiple hosts listed in a file named "hostfile.txt" every 10 seconds:
______________________________________________
./dumpProcStat my_process 10 hostfile.txt
______________________________________________


Note
The script requires SSH access to remote hosts if a hostfile is provided and uses key-based authentication. Ensure that you have the necessary permissions and SSH keys set up correctly to connect to the remote hosts.
The script relies on system commands such as pgrep, ps, and date to retrieve process statistics. Ensure these commands are available and accessible on your system.
