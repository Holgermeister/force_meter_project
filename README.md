# ForceMeter GUI

## Architecture

- Overview: Tkinter-based desktop GUI that orchestrates mechanical tests in a background process, streams readings back via queues, and plots Force–Displacement in real time with Matplotlib.
- Entry Point: GUI bootstrap and layout in [GUI_main.py](GUI_main.py); spawns tests using `multiprocessing` through [Auxiliary.safe_call](GUI_classes/Auxiliary.py) -> [force_test.main](force_test.py).
- Panels:
  - Top bar: [TopRowPanel](GUI_classes/TopRowPanel.py) — ports, Z-move, move size, emergency stop, reset/save plot.
  - Parameters: [ParameterPanel](GUI_classes/ParameterPanel.py) — configure params, generate config JSON, start tests; fields adapt to test type.
  - Past configs: [PastConfigPanel](GUI_classes/PastConfigPanel.py) — list/select/delete/refresh previous configs.
  - Plot: [PlotPanel](GUI_classes/PlotPanel.py) — embedded Matplotlib plot; auto-refresh ~25 FPS; save image.
  - Test reports: [TestReportPanel](GUI_classes/TestReportPanel.py) — list/invalidate/delete JSON reports.
  - Custom tests: [CustomTestBuilder](GUI_classes/CustomTestBuilder.py) — send stepwise moves during a run; save/upload reusable recipes.
- IPC: [Communication](GUI_classes/Communication.py) exposes `msg_queue` (data/events from test process) and `cmd_queue` (commands to the process); accumulates series for plotting.
- Data model: [DataJson](GUI_classes/DataJson.py) defines `TestConfig` and `custom_test_recipe` (JSON-serializable).
- Storage: configs (GUI_classes/configs), custom tests (GUI_classes/custom_tests), reports (GUI_classes/test_reports), plots (GUI_classes/plots).
