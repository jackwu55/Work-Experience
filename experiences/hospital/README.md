# 醫院系統專案總覽
本系統整合與本人在醫院單位任職期間所經手之多項臨床系統、後台批次、報表模組等開發經驗，涵蓋精神科病歷系統、生物資料庫維護、心理與社工報告電子化、中醫藥囑系統查詢與排班功能以及醫療影像上傳程式調整與申報合格率提升等關鍵專案。

鑑於上述專案多涉及病歷資料結構、健保署申報流程及單位內部作業設計，為遵守職業道德與保密義務，以下內容僅節錄代表性程式邏輯片段作為技術展示，敏感資訊（如欄位、帳號、實體結構等）皆已去識別化，請僅供參考。
以下為部分技術示範及功能說明，涵蓋列印功能、表單傳送及列印等重複性功能。  

---
## 代表功能範例 

以下展示內容為我於醫療單位任職期間，實際參與並負責開發與維護的部分功能模組。由於完整功能涉及院內病歷系統、申報作業與單位內部流程設計，為遵守保密義務與醫療機構相關規範，僅擇選具代表性且具一般性技術參考價值之模組進行說明。

針對視覺樣式微調、欄位順序優化、系統設定修改等細部內容，雖為日常維護之必要工作，惟不易體現整體系統邏輯或效能改善能力，因此不列入本次展示內容重點。

本區展示重點如下：

取得病患基本資料：AJAX 非同步介接後端資料，並動態填入欄位。

依參數取得遠端 SMB 伺服器檔案並輸出：由使用者提供參數動態組成路徑，下載 PDF、Excel 等病歷或報告檔案。

HTML 內容轉 PDF 並儲存至 SMB 檔案伺服器：將護理紀錄等內容進行版面轉換與文字編碼處理，生成 PDF 並儲存至網路磁碟。

醫療影像批次上傳程式維護與效能提升（文字說明）：本功能負責將醫療影像（如超音波、核醫影像等）依健保署規範，在 24 小時內完成上傳並通過審核。接手此項目初期發現舊有程式因判斷錯誤導致部分檔案缺漏、資料格式異常，進而影響月報合格率。

我在任內完成以下重構與優化：

與合作廠商協調取得正確影像欄位定義及命名規則。

針對資料庫操作進行調整，強化連線釋放機制，避免批次處理中連線資源占用過多導致遺失。

將錯誤日誌與上傳結果進行每日比對，自動標記異常紀錄以供申報同仁確認與修正。

透過上述措施，合格率自原本平均 65%～75%，穩定提升至 90% 以上，並持續維持穩定品質。

⚠️ 鑒於上傳格式、影像來源、院內與健保署溝通規則均屬醫療作業核心流程，相關程式碼與範例無法對外公開，僅以文字方式敘述。

## 代表功能範例 — 取得病患基本資料

### 流程文字描述
- 使用者於前端輸入資料（或其他識別碼）。
- 系統以 AJAX 非同步方式向後端 API 發出請求。
- 後端根據請求參數查詢資料庫。
- 回傳 JSON 格式資料至前端。
- 前端將資料自動填入對應表單欄位，提升使用者操作效率。

```javascript
// 取得病患基本資料
function getData(pa1) {
  $.ajax({
    url: '${pageContext.request.contextPath}/getData.do',
    data: {
      method: "getData",
      hhisnum: pa1
    },
    type: "POST",
    dataType: "json",
    async: false,
    contentType: "application/x-www-form-urlencoded;charset=utf-8",
    success: function(data) {
      $("#result1").val(data.result1);
      $("#result2").val(data.result2);
      $("#result3").val(data.result3);
      $("#result4").val(data.result4);
    },
    error: function(xhr) {
      alert("取得資料過程中，程式發生錯誤!!!\nerror code:" + xhr.status, 2);
    }
  });
}

```

---

## 依參數取得遠端 SMB 伺服器檔案並輸出

根據參數組合出 PDF 或 Excel 路徑，並從 SMB 共享資料夾讀取後回傳給前端瀏覽器，支援內嵌預覽與下載。

### 流程文字描述

將檔案串流回傳前端供使用者瀏覽或下載。
- 從前端接收檔案名稱、路徑、日期及讀取標記等參數。
- 判斷檔案所在共享資料夾路徑，組合 SMB 網址。
- 使用 NTLM 認證建立 SMB 連線。
- 取得檔案內容並根據副檔名設定 HTTP 回應標頭（PDF、Excel、Word）。

### 範例程式碼：

```java
String me = "showPDF.jsp";

try {
    String parameter1 = request.getParameter("parameter1"); // fileName
    String parameter2 = request.getParameter("parameter2"); // path
    String parameter3 = request.getParameter("parameter3"); // nowDate
    String parameter4 = request.getParameter("parameter4"); // readRecord

    String fileName = parameter1;
    String path = parameter2;
    String nowDate = parameter3;
    String readRecord = parameter4;

    if (readRecord != null) {
        if (readRecord.equals("Y")) {
            fileName = "\\\\pc\\system1\\pdf\\" + nowDate + "\\" + path + ".pdf";
        } else if (readRecord.equals("system2")) {
            fileName = "\\\\pc\\system2\\" + path + ".pdf";
        }
    } else {
        fileName = "\\\\pc\\system1\\pdf\\" + nowDate + "\\" + path + ".pdf";
    }

    fileName = "smb:" + fileName.replace("\\", "/");

    NtlmPasswordAuthentication nt = new NtlmPasswordAuthentication("", "account", "password");

    SmbFile sf = new SmbFile(fileName, nt);
    int length = sf.getContentLength();
    byte[] buffer = new byte[length];
    SmbFileInputStream in = new SmbFileInputStream(sf);

    if (fileName.lastIndexOf(".pdf") > 0) {
        response.setContentType("application/pdf");
    } else if (fileName.lastIndexOf(".doc") > 0) {
        response.setContentType("APPLICATION/OCTET-STREAM");
        response.setHeader("Content-disposition", "attachment; filename=\"" + fileName + "\"");
    } else if (fileName.lastIndexOf(".xls") > 0) {
        response.setContentType("application/vnd.ms-excel");
        response.setHeader("Content-Disposition", "inline;fileName=\"" + fileName + "\"");
    }

    OutputStream o = response.getOutputStream();
    int i = 0;
    while ((i = in.read(buffer)) > 0) {
        o.write(buffer, 0, i);
    }
    o.flush();
    o.close();
    out.clear();

} catch (Throwable e) {
    e.printStackTrace();
    System.out.println(me + " fail. " + e);
}
```

---

## HTML 內容轉 PDF 並儲存至 SMB 檔案伺服器

此功能負責將系統中的 HTML 格式紀錄（如護理紀錄、觀察記錄）轉換為 PDF 檔，並自動加入病患基本資訊與表頭樣式，最後儲存至遠端 SMB 路徑，供列印或存檔備查。

### 流程文字描述

- 系統接收欲轉換的 HTML 內容與病患相關資訊參數。
- 確認並建立遠端 SMB 目錄結構。
- 使用 iText PDF 套件及自訂字型將 HTML 內容解析並轉為 PDF。
- 加入自訂頁首頁尾等病歷相關資訊。
- 將生成的 PDF 存入 SMB 共享資料夾。

### 範例程式碼：

```java
public void createPDFHtml(
    String parameter1,   
    String parameter2,   
    String parameter3,   
    String parameter4,   
    String parameter5,   
    String parameter6,   
    String parameter7,   
    String parameter8,   
    String parameter9,   
    String parameter10,  
    String parameter11,  
    String parameter12   
) {
    SmbFile smbfile = CommonUtil.FileToSmbFile(parameter11);
    try {
        if (!smbfile.isDirectory()) {
            smbfile.mkdirs();
        }
    } catch (SmbException e1) {
        e1.printStackTrace();
    }

    String xmlPath = parameter11 + "/" + parameter12 + ".pdf";

    try {
        Document document = new Document(PageSize.LETTER, 60, 43, 101, 34);
        BaseFont bfChinese = BaseFont.createFont(
            "\\\\pc\\WebFont\\front.TTF", BaseFont.IDENTITY_H, BaseFont.NOT_EMBEDDED);
        FontFactory.register("\\\\pc\\WebFont\\front.TTF");

        PdfWriter writer = PdfWriter.getInstance(document,
            new SmbFileOutputStream(CommonUtil.FileToSmbFile(xmlPath)));
        writer.setStrictImageSequence(true);

        CommonForm common = new CommonForm();
        common.setBed(parameter6);
        common.setHhisnum(parameter2);
        common.setName(parameter4);
        common.setSect(parameter7);
        common.setSex(parameter8);
        common.setFindesc(parameter10);
        writer.setPageEvent(common);

        document.open();

        StyleSheet ss = new StyleSheet();
        ss.loadTagStyle("body", "face", "frontData");
        ss.loadTagStyle("body", "size", "13pt");
        ss.loadTagStyle("body", "encoding", "Identity-H");

        String content = handleTag("<br>" + parameter1);
        content = replaceTag(content, '<', "&lt;", '>', "&gt;");
        ArrayList htmlList = (ArrayList) HTMLWorker.parseToList(new StringReader(content), ss);

        Phrase ph = new Phrase();
        if (htmlList.size() == 0) {
            content = handleTagByReverse(content);
            Phrase phraseN = new Phrase(content, new Font(bfChinese, 10, Font.NORMAL));
            ph.add(phraseN);
        } else {
            for (Object obj : htmlList) {
                Element e = (Element) obj;
                for (Object chunkObj : e.getChunks()) {
                    ((Chunk) chunkObj).setSplitCharacter(new newSplit());
                }
                ph.add(e);
            }
        }

        document.add(ph);
        document.close();

    } catch (Throwable e) {
        e.printStackTrace();
    }
}
```

---

## 醫療影像批次上傳程式維護與效能提升
在院內醫療影像批次上傳作業中，過往系統存在因資料判斷錯誤導致部分影像檔案遺失未成功上傳的問題。此狀況嚴重影響申報合格率，當時合格率長期徘徊於65%至75%區間，亟需改善。

本人在接手該專案後，主動與合作廠商積極協商，釐清並取得符合健保署最新規範的正確資料格式，並依此修正批次上傳程式的邏輯與判斷條件，有效避免錯誤資料導致的上傳失敗。

此外，針對批次作業在資料庫連線數量管理上的瓶頸，我進一步優化系統確保每次批次作業後能適時釋放連線，防止因連線占用過多而造成後續上傳中斷或失敗的狀況。

經過上述多面向的持續調整與優化，系統合格率自本人接手後開始穩步提升，並於一年後獲得院內相關單位確認，平均合格率已從先前的約70%大幅提升至90%以上，達成專案預期目標，顯著提升醫院申報效率與數據品質。


## 專業守則與保密義務
以上展示僅為部分功能節錄，完整專案內容因應業務規範與職業保密責任未能公開，但均為實際上線功能
本人始終遵守職業倫理與保密義務，嚴格管控客戶資料與商業機密。  
本文內容為去識別化的代表性範例，旨在展現專業能力與工作實績，並尊重客戶權益。