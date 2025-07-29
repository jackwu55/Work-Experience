# 碩網軟體工程師專案總覽

本人於碩網任職期間，參與多項跨領域軟體開發專案，包含政府機關、企業客戶及國際廠商之需求客製化。  
專案涵蓋系統功能開發、自動化流程建置、伺服器壓力測試及優化，以及多種前後端整合與工具開發。

鑑於專案內容涉及客戶核心業務及保密資料，本文僅擷取代表性工作範疇與技術片段，去識別化處理，並強調專業技能與職業操守。

---

## 專案經歷重點

- **流程自動化**  
  包含專案打包、程式測試、弱點掃描及版本控管，確保開發流程順暢且符合安全規範。

- **客製化功能開發**  
  根據客戶需求調整與優化系統，涵蓋文字轉語音（TTS）、對話紀錄分類搜尋、文件審核功能、帳號密碼安全管理等。

- **伺服器壓力測試與問題修正**  
  針對服務器負載進行測試，快速定位並修正潛在性能瓶頸。

- **批次及腳本工具開發**  
  使用 Batch、Linux Shell Script 寫作資料統計與系統監控腳本，提升作業效率。

---

## 重要客戶與專案

- **日本林原**  
  IKM 系統搜尋功能與資料更新批次工具開發。

- **國稅局及電子發票系統**  
  Robot UI 調整、TTS及SST串接、對話紀錄多分類搜尋、文件審核及Tomcat日誌分析工具等。

- **中研院**  
  IKM 產品新增通知收件夾及文件稽催功能。

- **地方稅務局**  
  UI優化、Google Cloud Speech串接，支援多種檔案格式匯出。

- **富邦保險與東京電力**  
  對話搜尋與檔案異常修正，滿意度功能與帳號密碼安全機制開發。

---

## 技術堆疊與工具

- Java、Batch Script、Linux Shell Script  
- Google Cloud Speech API  
- Tomcat 伺服器管理與日誌分析  
- 前端 UI 修正與優化  
- 版本控管與持續整合工具

## 代表功能範例 

本區所展示內容為我在實務開發過程中所參與的部分模組與工具，由於完整功能涉及當時公司產品細節與客戶需求，為遵守保密義務與專案協議，本區僅節錄具代表性、技術性強的功能模組作為說明。

針對前端 UI 改進、樣式調整、設定維護等細部功能，雖日常開發中亦為常見任務，惟此類內容相對無法展現問題解決能力與系統架構理解，因此不列入本次展示重點。

主要展示內容包含：

generateMd5.sh：用於網站首頁關鍵檔案備份與產生 MD5 校驗碼。

compareMd5.sh：自動比對檔案是否遭異動，異動即還原並重新產生 MD5。

Tomcat Access Log 解析與回應時間計算工具：統計 /wise/chats-ajax.jsp 平均回應時間並評估效能。

語音轉換功能（TTS）整合：Google Cloud Speech API 整合與播放，前端語音播放按鈕與後端語音轉換路徑自動產生。


# generateMd5.sh 說明

此腳本用於備份指定目錄下的關鍵前端檔案，並生成對應的 MD5 檢查碼檔案，以便後續做檔案完整性驗證。

## 功能說明

- 將 `index.jsp`、`custom.css`、`web.js` 三個檔案複製到備份目錄 `/user/project/FrontPageBackup/backupFile`。
- 對上述三個檔案計算 MD5 值，並將結果輸出至相同備份目錄下，產生 `.md5` 檔案。

## 腳本內容範例

```sh
#!/bin/sh
cp /user/project/tomcat9/webapps/projectDirectory/chat/default/index.jsp /user/project/FrontPageBackup/backupFile
cp /user/project/tomcat9/webapps/projectDirectory/chat/default/css/custom.css /user/project/FrontPageBackup/backupFile
cp /user/project/tomcat9/webapps/projectDirectory/chat/default/js/web.js /user/project/FrontPageBackup/backupFile

md5sum /user/project/tomcat9/webapps/projectDirectory/chat/default/index.jsp > /user/project/FrontPageBackup/backupFile/index.jsp.md5
md5sum /user/project/tomcat9/webapps/projectDirectory/chat/default/css/custom.css > /user/project/FrontPageBackup/backupFile/custom.css.md5
md5sum /user/project/tomcat9/webapps/projectDirectory/chat/default/js/web.js > /user/project/FrontPageBackup/backupFile/web.js.md5
```
---

## compareMd5.sh 說明
此腳本用於比對當前目錄下的前端檔案與備份目錄中的檔案是否一致，若檔案被更動，將自動從備份恢復，並更新 MD5 紀錄，最後將結果寫入日誌。

## 功能說明
- 定義三組檔案路徑陣列：前端原始檔案路徑 frontFile、備份檔案路徑 md5File、與其對應的 MD5 檔案。

- 逐一比較前端檔案的 MD5 值與備份的 MD5 值。

- 若檔案有變動，則將備份檔案複製回前端路徑，更新 MD5 檔案。

- 執行結果包含成功復原或無異動的訊息，皆記錄於指定日誌檔中
```
#!/bin/sh
CurrDir=$(pwd)
Today=$(date +%Y%m%d)
time=$(date +%H:%M:%S)
Log_Path=${CurrDir}/log/
Log_File=CompareResult.${Today}.txt

frontFile=(/user/project/tomcat9/webapps/projectDirectory/chat/default/index.jsp /user/project/tomcat9/webapps/projectDirectory/chat/default/css/custom.css /user/project/tomcat9/webapps/projectDirectory/chat/default/js/web.js)
md5File=(/user/recovery/backupFile/index.jsp /user/recovery/backupFile/custom.css /user/recovery/backupFile/web.js)
len=${#frontFile[@]}

for ((i=0; i < len; i++));
do
    if [[ $(cat ${md5File[$i]}.md5 ) != $(md5sum -b ${frontFile[$i]}) ]]; then
        echo ${frontFile[$i]} "has been change,Starting Recovery file"
        echo ${time} ${frontFile[$i]} "has been change,Starting Recovery file" >> ${Log_Path}${Log_File}
        cp ${md5File[$i]} ${frontFile[$i]}
        md5sum ${frontFile[$i]} > ${md5File[$i]}.md5
        if [[ $(cat ${md5File[$i]}.md5 ) == $(md5sum -b ${frontFile[$i]}) ]]; then
            echo ${time} ${frontFile[$i]} "Recovery Success" >> ${Log_Path}${Log_File}
        fi
    else
        echo ${time} ${frontFile[$i]} "Compare OK" >> ${Log_Path}${Log_File}
    fi
done

```
---

## Tomcat Access Log 解析與回應時間計算工具

本工具為 Java 程式，專門用於從 Tomcat 伺服器的 access log 中擷取特定 API (`/wise/chats-ajax.jsp`) 的請求紀錄，並計算回應時間的平均值及統計低於3秒的請求比例。

---

## 功能說明

- **整合兩台伺服器（AP1、AP2）指定月份的 access log**  
  將指定路徑下兩台伺服器當月所有日誌檔案整合為一份檔案，過濾出包含 `/wise/chats-ajax.jsp` 請求的日誌行。

- **計算回應時間**  
  讀取合併後的日誌檔案，擷取每筆請求的回應時間，統計：
  - 總回應時間和請求數量
  - 平均回應時間
  - 回應時間小於3秒的請求數及比例

---

## 使用說明

### 執行命令
```
// 省略 import 區塊

public class countResponseTime {

    public static void main(String[] args) throws IOException {
        // 取得年月與輸出路徑
        Date date = new Date();
        SimpleDateFormat getYearFormat = new SimpleDateFormat("yyyy");
        int monthNum = date.getMonth();
        String year = monthNum == 0 ? String.valueOf(Integer.valueOf(getYearFormat.format(date)) - 1): getYearFormat.format(date);
        String outPath = args[0]+year+"年_"+(monthNum == 0 ? "12" : String.valueOf(monthNum))+"月份accesslog_ap1+ap2.txt";

        // 合併日誌與計算回應時間
        generateApLog(args[0], year, monthNum, outPath);
        calculateResponseTime(outPath);
    }

    private static void generateApLog(String arg, String year, int monthNum, String outPath) throws IOException {
        // 依日期串接路徑，讀取AP1及AP2的日誌，擷取特定請求行，寫入合併檔案
        // 省略具體程式碼
    }

    private static void calculateResponseTime(String outPath) throws IOException {
        // 讀取合併檔案，解析回應時間，計算平均值與統計
        // 省略具體程式碼
    }
}

```
---

## 前後端語音轉換功能（TTS）整合
本功能整合第三方語音合成服務（如 Google Cloud Speech），可將系統內指定文字轉換為語音播放，並以 .wav 音訊格式回傳至前端，實現即時語音回饋。

## 語音播放觸發（JavaScript）

$(document).on('click', '.btnTts', function() {
    $(this).button('loading'); // 顯示載入動畫

    var parameter1 = $(this).attr('data-id'); // 取得對話 QA ID
    var timestamp = new Date().getTime();

    $.get(contextPath + '/tts?qaId=' + parameter1 + '&ts=' + timestamp, function(data) {
        var audio = new Audio();
        audio.src = contextPath + '/texttospeech/' + data + '.wav';
        audio.load();
        audio.play();

        audio.addEventListener('ended', function() {
            $('.btnTts').button('reset'); // 播放結束後還原按鈕
        });
    });
});


## 後端：產生語音檔並回傳網址（Java）

public static String synthesizeTextAndReturnUrl(Tenant tenant, String text) {
    String fileName = synthesizeTextAndReturnFilename(tenant, text);
    
    if (fileName != null) {
        String host = SystemConfig.getHostname();
        String context = SystemConfig.getContextPath();
        String fallbackPath = host + context + "/audioCache";

        String configuredUrl = null;

        // 擷取租戶中指定的語音服務網址
        if (tenant.hasConfig("tts")) {
            JSONObject config = tenant.getJsonConfig("tts");
            configuredUrl = config.optString("ttsWebCacheUrl");
        }

        return (configuredUrl != null ? configuredUrl : fallbackPath) + "/" + fileName + ".wav";
    }

    return null;
}


## 語音檔產生邏輯（語系與語速配置）

public static String synthesizeTextAndReturnFilename(Tenant tenant, String text) {
    String[] ttsSetting = TTSService.getDefaultSetting("zh_TW");
    String speakingRate = "default";

    // 若租戶有指定語音參數，優先使用
    if (tenant != null) {
        String[] tenantSetting = TTSService.getLocaleSpecificSetting(tenant.getLocale());
        if (tenantSetting != null) {
            ttsSetting = tenantSetting;
        }

        if (tenant.hasConfig("tts")) {
            JSONObject cfg = tenant.getJsonConfig("tts");
            speakingRate = cfg.optString("speakingRate", "default");
        }
    }

    // 語音合成處理邏輯（與第三方服務串接）
    // ...省略具體程式碼...

    return generatedFilename; // 回傳語音檔案名稱（不含副檔名）
}

## 專業守則與保密義務
以上展示僅為部分功能節錄，完整專案內容因應業務規範與職業保密責任未能公開，但均為實際上線功能
本人始終遵守職業倫理與保密義務，嚴格管控客戶資料與商業機密。  
本文內容為去識別化的代表性範例，旨在展現專業能力與工作實績，並尊重客戶權益。
