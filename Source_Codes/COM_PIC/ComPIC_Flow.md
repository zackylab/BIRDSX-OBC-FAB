```mermaid
graph TD
    subgraph Initialization [初期化処理]
        A[Start] --> B("<b>Settings()</b><br>各種ペリフェラルと変数の初期設定を実行");
    end

    subgraph "Main Loop [while(true)]"
        direction TB
        B --> C("UART受信バッファをチェック<br><b>CHECK_UART_INCOMING_FROM_NEW_TRX()</b><br><b>CHECK_UART_INCOMING_FROM_OLD_TRX()</b>");
        C --> D("ResetPICとの定期通信とCWビーコン送信<br><b>COMUNICATE_WITH_RSTPIC_EVERY90SEC...()</b>");
        
        D --> E("NEWTRXの受信データをコマンドとして抽出<br><b>EXTRACTING_NEW_TRANCEIVER_COMMAND()</b>");
        E --> F{"NEWTRXのコマンドが有効か？<br>(ヘッダ、ID、フッタを確認)"};
        
        F -- Yes --> G["[コマンド処理]<br>GS_CMD.c内の<br>該当する各種コマンド処理関数を呼び出す"];
        G --> H("NEWTRX関連のバッファと変数をクリア<br><b>CLEAR_DATA_ARRAY()</b>, NEWTRX_RPL = 0");
        
        F -- No --> I("OLDTRXの受信データをコマンドとして抽出<br><b>EXTRACTING_OLD_TRANCEIVER_COMMAND()</b>");
        H --> I;

        I --> J{"OLDTRXのコマンドが有効か？<br>(ヘッダ、ID、フッタを確認)"};
        
        J -- Yes --> K["[コマンド処理]<br>GS_CMD.c内の<br>該当する各種コマンド処理関数(_)を呼び出す"];
        K --> L("OLDTRX関連のバッファと変数をクリア<br><b>CLEAR_DATA_ARRAY()</b>, OLDTRX_RPL = 0");

        J -- No --> M("ループ終端処理<br><b>delay_ms(10)</b><br><b>RESTART_WDT()</b><br><b>MLC++</b>");
        L --> M;
        
        M --> C;
    end
    ```