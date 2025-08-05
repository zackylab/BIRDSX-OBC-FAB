```mermaid
classDiagram
    direction TD

    class StartPIC_Main {
        +unsigned int16 RPIC_RESPOND_SEC_COUNTER
        +unsigned int16 MLC
        +unsigned int16 SEC_COUNT
        +unsigned int8 ONEHOUR_FLAG
        +char BB, CC
        +void main()
    }
    %%note for StartPIC_Main "STARPIC.c<br>StartPICのメインループとシステムの状態管理を担当する中心的なモジュール。"

    class StartPIC_Functions {
        +const char ON = 0x69
        +const char OFF = 0x96
        +char POWER_LINE_STATUS
        +unsigned int8 RPIC_TO_SPIC_ARRAY[]
        +unsigned int8 RP_Buffer[]
        +void SERIAL_ISR1()
        +unsigned int8 RPic_Available()
        +unsigned int8 RPic_Read()
        +void CHECK_UART_INCOMING_FROM_RESET_PIC()
        +void TURN_OFF_ALL_POWER_LINES()
        +void TURN_ON_ALL_POWER_LINES()
        +void CLEAR_RPIC_TO_SPIC_ARRAY()
    }
    %%note for StartPIC_Functions "StartPIc_Funtions.c<br>電源ライン制御、ResetPICとのUART通信、および関連するユーティリティ関数とグローバル変数を提供するモジュール。"

    %%' --- Relationships ---
    StartPIC_Main --> StartPIC_Functions : uses
    ```