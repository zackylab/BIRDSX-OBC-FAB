```mermaid
classDiagram
    direction TABLE

    class ResetPIC_Main {
        +unsigned int16 MLC
        +void settings()
        +void main()
    }
    %%note for ResetPIC_Main "ResetMain.c<br>ResetPICのメインループと初期設定。各モジュールを統括する中心的なコントローラ。"

    class ResetPIC_Settings {
        +unsigned int16 MPIC_TIME_COUNTER
        +unsigned int8 NUMOF_MPIC_RST
        +unsigned int16 CPIC_TIME_COUNTER
        +unsigned int8 NUMOF_CPIC_RST
        +unsigned int8 MPIC_TO_RPIC_ARRAY[]
        +unsigned int8 RPIC_TO_MPIC_ARRAY[]
        +unsigned int8 CPIC_TO_RPIC_ARRAY[]
        +unsigned int8 RPIC_TO_CPIC_ARRAY[]
        +void RST_EXT_WDT()
        +unsigned int8 MPic_Available()
        +unsigned int8 MPic_Read()
        +void MPic_flush()
        +unsigned int8 CPic_Available()
        +unsigned int8 CPic_Read()
        +void CPic_flush()
        +unsigned int8 SPic_Available()
        +unsigned int8 SPic_Read()
        +void SPic_flush()
        +void printline()
        +void CLEAR_DATA_ARRAY(array[], size)
    }
    %%note for ResetPIC_Settings "ResetPIC_Functions.c<br>グローバル変数、通信バッファ、UARTのISRと低レベルドライバ関数を定義。"

    class Power_ADC_Control {
        +unsigned int8 POWER_LINE_STATUS
        +unsigned int8 RESET_TIME
        +void MP_CP_BuckBoost(status)
        +void MainPic_Power(status)
        +void ComPic_Power(status)
        +void _3V3Power_Line1(status)
        +void _3V3Power_Line2(status)
        +void _5V0Power_Line(status)
        +void Unreg1_Line(status)
        +void Unreg2_Line(status)
        +void SYSTEM_RESET()
        +void SYSTEM_RESET_24H()
        +unsigned int16 Measure_Raw_voltage()
        +unsigned int16 Measure_3V3_1_current()
        +unsigned int16 Measure_3V3_2_current()
        +unsigned int16 Measure_5V0_current()
        +unsigned int16 Measure_UNREG_1_current()
        +unsigned int16 Measure_UNREG_2_current()
    }
    %%note for Power_ADC_Control "ADC_Power_Lines_Control.c<br>電源ラインのON/OFF制御、システムリセット、ADCによる電圧・電流測定など、ハードウェア制御を担う。"

    class RTC_Functions {
        +unsigned int8 year, month, day, hour, minute, second
        +void Set_RTC(y, mo, d, h, mi, s)
        +void RTC_Function()
        +void PRINT_POWER_LINE_STATUS()
        +void Print_RTC()
    }
    %%note for RTC_Functions "RTC_fun.c<br>タイマ割り込みを利用したソフトウェアRTC（リアルタイムクロック）の管理と表示。"

    class RPIC_MPIC_Handler {
        +void CHECK_UART_INCOMING_FROM_MAIN_PIC()
        +void PRINT_RECIVED_COMMAND_FROM_MAIN_PIC()
        +void MONITOR_MAIN_PIC_90SEC_COMUNICATION(time)
        +void RESEPOND_TO_MPIC_90SEC_CMD()
        +void UPDATE_RTC_BY_MAIN_PIC_CMD()
        +void POWER_LINE_CONTROL_USING_MAIN_PIC_CMD()
        +void RESET_SATELLITE_CMD()
        +void TURN_ON_UNREG_2_LINE_FOR_ANT_DEPLOYMENT()
    }
    %%note for RPIC_MPIC_Handler "RPIC_MPIC.c<br>MainPICとの通信コマンドの解釈と応答処理を行う高レベルなハンドラ。"

    class RPIC_CPIC_Handler {
        +void CHECK_UART_INCOMING_FROM_COM_PIC()
        +void PRINT_RECIVED_COMMAND_FROM_COM_PIC()
        +void MONITOR_COM_PIC_90SEC_COMUNICATION(time)
        +void RESEPOND_TO_CPIC_90SEC_CMD()
        +void UPDATE_RTC_BY_COM_PIC_CMD()
        +void SENDING_TIME_TO_COMPIC()
    }
    %%note for RPIC_CPIC_Handler "RPIC_CPIC.c<br>ComPicとの通信コマンドの解釈と応答処理を行う高レベルなハンドラ。"

    class RPIC_STARTPIC_Handler {
        +void INFORM_WORKING_TO_START_PIC()
    }
    %%note for RPIC_STARTPIC_Handler "RPIC_STARTPIC.c<br>START PIC（起動用マイコン）へ動作中であることを通知する機能。"

    %%' --- Relationships ---
    ResetPIC_Main --> ResetPIC_Settings : uses
    ResetPIC_Main --> Power_ADC_Control : uses
    ResetPIC_Main --> RTC_Functions : uses
    ResetPIC_Main --> RPIC_MPIC_Handler : uses
    ResetPIC_Main --> RPIC_CPIC_Handler : uses
    ResetPIC_Main --> RPIC_STARTPIC_Handler : uses

    RPIC_MPIC_Handler --> Power_ADC_Control : uses
    RPIC_MPIC_Handler --> ResetPIC_Settings : uses
    RPIC_MPIC_Handler --> RTC_Functions : uses

    RPIC_CPIC_Handler --> ResetPIC_Settings : uses
    RPIC_CPIC_Handler --> RTC_Functions : uses

    RTC_Functions --> Power_ADC_Control : uses
    RTC_Functions --> ResetPIC_Settings : uses
    ```