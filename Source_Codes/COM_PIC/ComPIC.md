```mermaid
classDiagram
    direction TABLE

    class CompicMain {
        +char SAT_ID
        +int OLDTRX_RPL
        +int NEWTRX_RPL
        +int16 CW_INTERVAL
        +char B_6
        +char B_7
        +void Settings()
        +void main()
    }
    %%note for CompicMain "CompicMain.c\nシステムのメインループと初期設定を管理する中心的なモジュール。"

    class ComPic_Settings {
        +<<define>> TXMODE_PIN
        +<<define>> TXDONE_PIN
        +<<define>> TRXPWR_PIN
        +<<define>> RSTPIC_RESTART_PIN
        +unsigned int16 MLC
        +unsigned int CW_DATA[16]
        +unsigned int CW_DATA_TO_NEWTRX[55]
        +unsigned int8 CW_FLAG
        +unsigned int8 NEWTRX_CW
        +unsigned int8 OLDTRX_CW
        +unsigned int8 NEWTRX_1PCKT
        +unsigned int8 OLDTRX_1PCKT
        +unsigned int32 TLE_ADDRESS
        +unsigned int8 NEW_TRX_STATUS
        +unsigned int8 ON_
        +unsigned int8 OFF_
        +unsigned int8 RPIC_TO_CPIC_ARRAY[55]
        +unsigned int8 CPIC_TO_RPIC_ARRAY[55]
        +unsigned int8 NEWTRX_CMD_BUFFER[60]
        +unsigned int8 NEWTRX_TO_CPIC_ARRAY[60]
        +unsigned int8 CPIC_TO_NEWTRX_ARRAY[110]
        +unsigned int8 OLDTRX_CMD_BUFFER[60]
        +unsigned int8 OLDTRX_TO_CPIC_ARRAY[60]
        +unsigned int8 CPIC_TO_OLDTRX_ARRAY[110]
        +unsigned int8 MPIC_TO_CPIC_ARRAY[55]
        +unsigned int8 CPIC_TO_MPIC_ARRAY[55]
        +unsigned int8 GS_callsign_leter_1
        +unsigned int8 ST_callsign_leter_1
        +void printline()
        +void CLEAR_DATA_ARRAY(array[], size)
        +unsigned int16 CALCULATE_CRC(data[], number)
        +void FM_WRITE_ENABLE()
        +void FM_SECTOR_ERASE(address, size, delay)
        +void FM_BYTE_WRITE(address, data)
        +unsigned int8 FM_BYTE_READ_(ADDRESS)
        +void SERIAL_ISR1()
        +void SERIAL_ISR2()
        +void SERIAL_ISR3()
        +void SERIAL_ISR4()
    }
    %%note for ComPic_Settings "ComPic_Settings.c\nシステム全体で使用される設定値、グローバル変数、共通ユーティリティ関数（CRC計算、Flashメモリ操作など）、およびUARTの割り込みサービスルーチンを定義。"

    class OLDTRX_Functions {
        +void OLD_TRX_RX_MODE()
        +void OLD_TRX_TX_MODE()
        +void OLD_TRX_CW_MODE()
        +void CHECK_UART_INCOMING_FROM_OLD_TRX(header)
        +void EXTRACTING_OLD_TRANCEIVER_COMMAND(sync_byte)
        +void OLDTRX_SEND_ACKNOWLEDGEMENT(mode)
        +void OLD_TRX_SEND_DATA_PACKETS_TO_GS(START_ADDRESS, NUM_OF_PACKETS)
        +void OLD_TRX_SEND_1PCKT_BEACON()
    }
    %%note for OLDTRX_Functions "OLDTRX_Functions.c\n旧型トランシーバーとの通信（モード設定、コマンド受信・解析、データ送信）を管理。"

    class NEWTRX_Functions {
        +void NEW_TRX_RX_MODE()
        +void NEW_TRX_TX_MODE()
        +void LOAD_CW_MODE_AND_NEW_TRX_STATUS()
        +void ENABLE_DISABLE_NEW_TRX_POWER()
        +void CHECK_UART_INCOMING_FROM_NEW_TRX(header)
        +void EXTRACTING_NEW_TRANCEIVER_COMMAND(sync_byte)
        +void DELAY_UNTILL_TRX_FINISH_TRANSMITTING_A_PACKET()
        +void NEWTRX_SEND_ACKNOWLEDGEMENT(mode)
        +void NEW_TRX_SEND_DATA_PACKETS_TO_GS(START_ADDRESS, NUM_OF_PACKETS)
        +void NEW_TRX_SEND_1PCKT_BEACON()
    }
    %%note for NEWTRX_Functions "NEWTRX_Functions.c\n新型トランシーバーとの通信（モード設定、電源制御、コマンド受信・解析、データ送信）を管理。"

    class CW_Morse {
        +<<define>> CW_PIN
        +void CH_SPACE()
        +void WORD_SPACE()
        +void DIT()
        +void DAH()
        +void CW_A()
        +void CW_0()
        +void CW_LTR(CWL)
        +void SEND_CW_NEWTRX()
        +void SEND_CW_OLDTRX()
    }
    %%note for CW_Morse "CW_Morse.c\nモールス信号（CW）の生成と、各トランシーバー経由での送信処理を管理。"

    class CPic_RPic {
        +void CHECK_UART_INCOMING_FROM_RESET_PIC()
        +void PRINT_RECIVED_COMMAND_FROM_RESET_PIC()
        +void COMUNICATION_WITH_RST_PIC_AND_WAIT_FOR_RESPONE(numof_times, time_delay, WT)
        +void COMUNICATE_WITH_RSTPIC_EVERY90SEC_AND_ASK_CW_DATA_FROM_MAINPIC(time)
        +void UPDATE_RESET_PIC_TIME_BY_COM_PIC(yr, mo, dy, hr, mn, sc)
    }
    %%note for CPic_RPic "CPic_RPic.c\nReset PICとのUART通信を管理。定期通信、時刻同期などを行う。"

    class CPic_MPic {
        +void CHECK_UART_INCOMING_FROM_MAIN_PIC()
        +void PRINT_RECIVED_COMMAND_FROM_MAIN_PIC()
        +void COMUNICATION_WITH_MAIN_PIC_AND_WAIT_for_RESPONE(numof_times, time_delay, wait_time)
        +void GET_FM_ACCESS_FROM_MAIN_PIC(sec_)
    }
    %%note for CPic_MPic "CPic_MPic.c\nMain PICとのUART通信を管理。Flashメモリのアクセス権要求などを行う。"

    class GS_CMD {
        +void PACKETS_DOWNLOAD_WITHOUT_FM_ACCESS()
        +void _PACKETS_DOWNLOAD_WITHOUT_FM_ACCESS()
        +void PACKETS_DOWNLOAD_WITH_FM_FULL_ACCESS()
        +void _PACKETS_DOWNLOAD_WITH_FM_FULL_ACCESS()
        +void DATA_DOWNLOAD_FROM_MAIN_FM_OR_MSN_FM()
        +void _DATA_DOWNLOAD_FROM_MAIN_FM_OR_MSN_FM()
        +void FLASH_MEMORY_SECTOR_ERASE()
        +void _FLASH_MEMORY_SECTOR_ERASE()
        +void TRANSFERING_ANTENNA_RELATED_CMD_TO_MAINPIC()
        +void _TRANSFERING_ANTENNA_RELATED_CMD_TO_MAINPIC()
        +void SENDING_APRS_MISSION_CMD_TO_MAINPIC()
        +void _SENDING_APRS_MISSION_CMD_TO_MAINPIC()
        +void CONTROL_CW_TX_MODE()
        +void _CONTROL_CW_TX_MODE()
        +void CONTROL_NEW_TRX_POWER()
        +void _CONTROL_NEW_TRX_POWER()
        +void _TURN_ON_CW_FOR_ONE_MINUTE()
        +void TLE_DOWNLINK_MISSION()
        +void _TLE_DOWNLINK_MISSION()
        +void UPDATE_RST_PIC_TIME_BY_GS_CMD()
        +void _UPDATE_RST_PIC_TIME_BY_GS_CMD()
        +void SEND_CUURENT_TIME_OF_THE_SATELLITE()
        +void _SEND_CUURENT_TIME_OF_THE_SATELLITE()
        +void RESTART_RESET_PIC_BY_GS_CMD()
        +void _RESTART_RESET_PIC_BY_GS_CMD()
        +void FAB_KILL_SWITCH_CONTROL()
        +void _FAB_KILL_SWITCH_CONTROL()
        +void _UPDATING_NEWTRX_SETTINGS_BY_GS_CMD()
        +void TRANSFER_RESERVATION_CMD_TO_MAINPIC()
        +void _TRANSFER_RESERVATION_CMD_TO_MAINPIC()
    }
    %%note for GS_CMD "GS_CMD.c\n地上局から受信したコマンドを解釈し、対応する処理を実行する。関数名のプレフィックスなしが新型TRX経由、`_`付きが旧型TRX経由のコマンド処理に対応。"

    %%' Relationships
    CompicMain --> OLDTRX_Functions : uses
    CompicMain --> NEWTRX_Functions : uses
    CompicMain --> CPic_RPic : uses
    CompicMain --> GS_CMD : uses
    CompicMain --> ComPic_Settings : uses

    GS_CMD --> NEWTRX_Functions : uses
    GS_CMD --> OLDTRX_Functions : uses
    GS_CMD --> CPic_MPic : uses
    GS_CMD --> CPic_RPic : uses
    GS_CMD --> ComPic_Settings : uses

    CPic_RPic --> CPic_MPic : uses
    CPic_RPic --> CW_Morse : uses
    CPic_RPic --> NEWTRX_Functions : uses
    CPic_RPic --> OLDTRX_Functions : uses

    CW_Morse --> OLDTRX_Functions : uses

    %%' All modules use settings and utilities
    OLDTRX_Functions ..> ComPic_Settings : uses
    NEWTRX_Functions ..> ComPic_Settings : uses
    CW_Morse ..> ComPic_Settings : uses
    CPic_RPic ..> ComPic_Settings : uses
    CPic_MPic ..> ComPic_Settings : uses
    ```