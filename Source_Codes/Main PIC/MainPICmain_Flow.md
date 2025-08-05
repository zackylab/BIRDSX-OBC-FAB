```mermaid
graph TD
    subgraph Initialization [初期化処理]
        A[Start] --> B("Delay_ms(1000)")
        B --> C("<b>setting()</b><br>割り込み, RTC, GPIOピンなどを初期化")
        C --> D(ブートメッセージをPCに出力)
        D --> E("EEPROM/Flashから各種設定値をロード<br><b>LOAD_ANTENNA_DEPLOYMENT_VALUES()</b><br><b>LOAD_RESERVATION_COMMANDS...()</b><br><b>LOAD_30DAY_COUNTER()</b>")
        E --> F("起動時のCWステータスを確認/更新<br><b>INCREACE_30DAY_COUNTER...()</b>")
    end

    subgraph "Main Loop [while(true)]"
        direction TB
        F --> G_LOOP_START("時刻表示 & アンテナ展開チェック<br><b>READ_MP_RTC_AND_PRINT()</b><br><b>DEPLOY_ANTENNA_SET_1()</b><br><b>DEPLOY_ANTENNA_SET_2()</b>")
        G_LOOP_START --> H("定期処理の実行<br><b>COMUNICATION_WITH_RST_PIC_90SEC()</b><br><b>CHECK_RESERVATION_COMMAND...()</b><br><b>RESTARTING_MISSIONS...()</b>")
        H --> I("UARTポートの受信チェック<br><b>CHECK_UART_INCOMING_FROM_DEBUG_PORT()</b><br><b>CHECK_UART_INCOMING_FROM_COM_PIC()</b>")
        
        I --> J{"ComPicコマンド受信 & 有効か？<br>if (CPIC_TO_MPIC_ARRAY...)"}
        
        J -- Yes --> K["[ComPicコマンド処理]<br>受信コマンドをPCに出力<br><b>GIVE_COMFM_ACCESS...()</b><br><b>DATA_COPY...()</b>など各種コマンド処理を実行"]
        K --> L("ComPic用受信バッファをクリア<br><b>CLEAR_DATA_ARRAY(CPIC_TO_MPIC_ARRAY,...)</b>")
        
        J -- No --> M
        L --> M{"Debugポートコマンド受信 & 有効か？<br>if (DEBUG_TO_MPIC_ARRAY...)"}

        M -- Yes --> N["[Debugコマンド処理]<br>受信コマンドをPCに出力<br><b>READ_FM_DATA...()</b><br><b>DEPLOY_ANTENNA...()</b>など各種コマンド処理を実行"]
        N --> O("Debugポート用受信バッファをクリア<br><b>CLEAR_DATA_ARRAY(DEBUG_TO_MPIC_ARRAY,...)</b>")

        M -- No --> P
        O --> P("ループ終端処理<br><b>CHECK_COMFM_ACCES_RELEASING()</b><br><b>MLC++</b>")
        
        P --> G_LOOP_START
    end
    ```