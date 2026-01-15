# ForceMeter GUI

## Architecture

### Overview: 
Tkinter-based desktop GUI that orchestrates mechanical tests in a background process, streams readings back via queues, and plots Force–Displacement in real time with Matplotlib. All code related to executing the force-displacement tests are keept in `ender_fdm` and its main file `force_test.py`. Inside the dirs `configs`, `test_reports`, `custom_tests` and `plots` are saved configuration files, JSON test reports, custom test recipes and saved plots respectively.

### Classes
Each class from the dir `GUI_classes` are initilized inside the GUI_main.py. The GUI_main.py holds most of the state information.

#### Auxiliary.py
Holds AUX function that does not take self, most importanly `safe_call()` that calls the main function of the force testing logic within force_test.py.

#### Communication.py
Keeps track of the two one directinal Queues used for commnication between the GUI and back-end testing logic. 
*msg_queue*: the GUI reads from this 
*cmd_queue*: the backend read from this, currently only inside the custom test function in ender_fdm/force_gauge.py.

### PlotPanel.py
Embeds a Matplotlib figure and periodically updates the Force–Displacement plot using data buffered in `Communication.tests`. Supports reset and saving plots.

### TopRowPanel.py
Controls ports and movement: display/setup ports, set move size, manual Z up/down, emergency stop (terminates the process), reset plot, save plot, change ports.

### PastConfigPanel.py
Lists previously saved configuration files; provides refresh, delete (single/all), and double-click to load a config and set it as `active_config`.

### CustomTestBuilder.py
Sends stepwise custom commands to the running test via `cmd_queue`; supports undo last move,     finish/save a `custom_test_recipe`, and upload/playback recipes for a selected config. Weeh uploading/rerunning a custom test the config used for the test, needs to be present in the dir `GUI_classes/configs`.

### TestReportPanel.py
Lists JSON test reports, supports invalidate (marks a JSON report unuseable) and delete; 

### DataJson.py
Defines the data models:
- **TestConfig**: Dataclass of test parameters (serial ports, motion settings, test options);     JSON-serializable.
- **custom_test_recipe**: Dataclass representing a named sequence of custom moves; JSON-          serializable.

### custom_msg.py
Defines message types used in custom testing:
- **custom_test_msg**: A single move instruction with `direction` and `z_inc`.
- **panic_msg**: An undo signal carrying the step to revert.

## Testing logic
Inside the `force_gauge.py` is the config from the GUI read and the test function according to the `Test_type` is executed and all parameters need for the test can be found in the config file.

Inside the `ender_fdm/force_gauge.py` is the main logic of each test type implemented. 






