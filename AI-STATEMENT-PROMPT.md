# 🏦 बैंक statement पढ़ने वाला प्रॉम्प्ट

PDF न मिले तो काग़ज़ की **फोटो** से भी statement चढ़ाई जा सकती है।

यही प्रॉम्प्ट app के **मिलान → 📋 AI से पेस्ट** में एक टैप पर कॉपी हो जाता है।

---

## कैसे इस्तेमाल करें

1. statement के पन्नों की **साफ़ फोटो** लें (एक बार में 2–4 पन्ने)
2. Claude या ChatGPT खोलें
3. नीचे वाला पूरा हिस्सा कॉपी करके चिपकाएँ, **और साथ में फोटो लगा दें**
4. जो JSON आए उसे कॉपी करें
5. app → **🏦 मिलान** → **📋 AI से पेस्ट** → चिपकाएँ → **जाँचकर जोड़ें**

⚠️ **app ख़ुद जाँच करेगा।** हर पंक्ति के बाद बची हुई रकम का हिसाब मिलाया जाएगा।
कोई अंक ग़लत पढ़ा गया होगा तो app **वही पंक्ति पकड़कर बता देगा** — इसलिए
ग़लती चुपचाप अंदर नहीं जा सकती।

---

```
तुम भारतीय बैंक statement पढ़ने वाले सहायक हो। साथ लगी statement की फ़ोटो
पढ़ो और सिर्फ़ JSON लौटाओ — कोई बात नहीं, कोई घेरा नहीं, बस JSON।

रूप बिल्कुल यही रखो:

{
  "account": { "name": string|null, "number": string|null, "bank": string|null },
  "period":  { "from": "YYYY-MM-DD"|null, "to": "YYYY-MM-DD"|null },
  "openingBalance": number|null,
  "transactions": [
    {
      "date":        "YYYY-MM-DD",
      "description": string,
      "chequeNo":    string|null,
      "utr":         string|null,
      "party":       string|null,
      "debit":       number|null,
      "credit":      number|null,
      "balance":     number|null
    }
  ]
}

नियम — ध्यान से पढ़ो, हर एक ज़रूरी है:

1. एक पंक्ति = एक सौदा। एक भी मत छोड़ो, एक भी दुबारा मत लिखो।
   पन्ने के क्रम में ही लिखो, ऊपर से नीचे।

2. debit और credit अलग-अलग खाने हैं। जो खाली है उसमें null लिखो।
   कभी दोनों मत भरो। "गया" = debit, "आया" = credit.

3. balance वही लिखो जो उस पंक्ति के आगे छपा है। "CR"/"DR" हटा दो, सिर्फ़
   संख्या रखो। यह सबसे ज़रूरी खाना है — इसी से जाँच होगी।

4. सब रकम सादे अंक: ₹ नहीं, कॉमा नहीं। "1,50,000.00" → 150000
   पैसे हों तो रखो: "90,004.72" → 90004.72

5. तारीख़ Post Date वाली लो। "02-04-2026" → "2026-04-02"

6. description में उस सौदे की सारी पंक्तियाँ जोड़कर लिखो, जैसी छपी हैं।
   एक सौदा तीन-चार लाइनों में फैला हो तो सबको एक ही description में डालो।

7. party = जिसे पैसा गया या जिससे आया, उसका नाम। इन रूपों को पहचानो:
   • "CHEQUE TRANSFER TO 0039867691276 OF Miss. SHIVANI ." → "SHIVANI"
   • "CAS CASH CHEQUE / MANOJ DAN"                        → "MANOJ DAN"
   • "Chq 917586 Sess 2 BALAJI TIMBER 000907701"          → "BALAJI TIMBER"
   • "CAS PRES CHQ / MINAXI AGENCY"                       → "MINAXI AGENCY"
   • "UPI/CR/032771344249/MANISH"                         → "MANISH"
   • "NEFT*UBIN0918628*0107144631 72*KV JALIPA PM SHRI*B" → "72 KV JALIPA PM SHRI B"
   नाम कहीं न लिखा हो (जैसे "REMT THRU CHQ / NEFT UTR NO: SBIN…") तो
   party में null लिखो — अंदाज़ा मत लगाओ। चेक नंबर से नाम बाद में जुड़ जाएगा।

8. chequeNo = "Cheque No/Reference" खाने में छपा नंबर (जैसे 917568)।
   न हो तो null।

9. utr = UTR / UPI transaction ID / NEFT reference — जो भी छपा हो
   (जैसे "SBIN426092856383" या "032771344249")। न हो तो null।

10. जो अंक साफ़ न दिखे उसे अंदाज़े से मत भरो — null लिखो। ग़लत अंक से
    पूरा हिसाब बिगड़ता है, खाली खाने से नहीं।

11. openingBalance = "BROUGHT FORWARD" वाली पंक्ति की रकम, अगर दिखे।

सिर्फ़ JSON लौटाओ।
```

---

## नमूना — जवाब ऐसा दिखेगा

```json
{
  "account": { "name": "BEST CHOICE TRADERS", "number": "43363032156",
               "bank": "State Bank of India" },
  "period": { "from": "2026-04-01", "to": "2026-07-29" },
  "openingBalance": 1783965.30,
  "transactions": [
    { "date": "2026-04-02",
      "description": "REMT THRU CHQ NEFT UTR NO: SBIN426092856383",
      "chequeNo": "917568", "utr": "SBIN426092856383", "party": null,
      "debit": 90000, "credit": null, "balance": 1693965.29 },
    { "date": "2026-04-03",
      "description": "CAS PRES CHQ Chq 917564 Sess 2 BHAWANI SHANKAR YUDHI 000753130",
      "chequeNo": "917564", "utr": null, "party": "BHAWANI SHANKAR YUDHI",
      "debit": 50400, "credit": null, "balance": 1343565.29 },
    { "date": "2026-04-06",
      "description": "DEP TFR NEFT*UBIN0918628*0107144631 72*KV JALIPA PM SHRI*B",
      "chequeNo": null, "utr": "0107144631", "party": "72 KV JALIPA PM SHRI B",
      "debit": null, "credit": 48404, "balance": 1391969.29 }
  ]
}
```

---

## 🛡️ app की जाँच

पेस्ट करते ही app हर पंक्ति का हिसाब मिलाएगा:

> पिछली बची रकम − गया + आया = इस पंक्ति की बची रकम

- **सब मिल गया** → «✅ 46 में से 46 पंक्तियों का हिसाब मिला» — जोड़ दीजिए
- **कोई पंक्ति न मिले** → app वही पंक्ति दिखाएगा, तब उस पन्ने की फोटो
  दोबारा भेजकर सिर्फ़ वही हिस्सा ठीक करवा लीजिए

इसलिए AI कोई अंक ग़लत पढ़े तो भी वह **चुपचाप हिसाब में नहीं घुस सकता**।

---

## 📌 ध्यान रखने की बातें

- **एक बार में 2–4 पन्ने** — ज़्यादा डालने पर AI बीच की पंक्तियाँ छोड़ने लगता है
- हर बार का JSON **अलग-अलग** पेस्ट करें, app अपने-आप जोड़ता जाएगा
- वही पन्ना दुबारा डालने से **कुछ दुबारा नहीं जुड़ेगा** (UTR/चेक नंबर से पहचान)
- app में यह डेटा **आपके अपने Claude/ChatGPT** से होकर आता है — app ख़ुद कभी
  बैंक का डेटा किसी AI को नहीं भेजता (यह `VISION.md` का पक्का नियम है)
