# ForceMeter GUI

## Architecture

- Overview: Tkinter-based desktop GUI that orchestrates mechanical tests in a background process, streams readings back via queues, and plots Force–Displacement in real time with Matplotlib.
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

```mermaid
flowchart TD
    A[App (GUI_main.py)] --> B[TopRowPanel]
    A --> C[ParameterPanel]
    A --> D[PastConfigPanel]
    A --> E[PlotPanel (Matplotlib)]
    A --> F[TestReportPanel]
    A --> G[CustomTestBuilder]
    A --> H[Communication]
    A -.spawn.-> P[Testing Process<br/>Auxiliary.safe_call -> force_test.main]
    H <-.msg_queue.-> P
    H <-.cmd_queue.-> P
    C -->|writes| FS1[GUI_classes/configs/*.json]
    G -->|saves| FS2[GUI_classes/custom_tests/*.json]
    F -->|lists| FS3[GUI_classes/test_reports/*.json]
    E -->|saves| FS4[GUI_classes/plots/*]
```
