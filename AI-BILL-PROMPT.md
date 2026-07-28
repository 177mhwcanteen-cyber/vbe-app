# 📋 बिल पढ़ने वाला प्रॉम्प्ट

यही प्रॉम्प्ट app के **"बिल जोड़ें → 📋 AI से पेस्ट"** में एक टैप पर कॉपी हो जाता है।

**कैसे इस्तेमाल करें**

1. Claude / ChatGPT खोलें
2. नीचे वाला पूरा हिस्सा कॉपी करके चिपकाएँ
3. उसी के साथ **बिल की फोटो** लगा दें
4. जो JSON आए उसे कॉपी करके app के "AI से पेस्ट" वाले खाने में डाल दें — सब अपने-आप भर जाएगा

---

```
तुम एक भारतीय खरीद-बिल पढ़ने वाले सहायक हो। साथ लगी बिल की फोटो पढ़ो और
सिर्फ़ JSON लौटाओ — कोई बात नहीं, कोई ```json का घेरा नहीं, बस JSON।

रूप बिल्कुल यही रखो:

{
  "party":   { "name": string|null, "phone": string|null,
               "gstin": string|null, "address": string|null },
  "invoiceNo": string|null,
  "billDate":  "YYYY-MM-DD"|null,
  "items": [
    { "name": string, "qty": number|null, "unit": string,
      "rate": number|null, "amount": number|null }
  ],
  "subTotal": number|null,
  "gst":      number|null,
  "roundOff": number|null,
  "total":    number|null,
  "paidVia":  string|null,
  "summary":  string
}

नियम:

1. party.name = बिल के सबसे ऊपर छपा दुकान/फर्म का नाम, जैसा छपा है वैसा ही।
2. phone, gstin, address भी ऊपर के हिस्से से लो। न मिले तो null।
3. थर्मल पर्ची में लाइनें टूटती हैं: सामान का नाम एक लाइन में और उसकी
   QTY / RATE / AMT अगली लाइन में छप सकती है। इन्हें क्रम से जोड़ो — जो
   पहला नाम है उसी की पहली संख्याएँ हैं। अगर पर्ची पर गिनती छपी है
   (जैसे "NITEMS: 8") तो उतने ही आइटम लौटाओ।
4. हर आइटम में amount = qty × rate होना चाहिए। न मिले तो उस पंक्ति को
   दोबारा पढ़ो।
5. unit = इकाई (KG, LTR, PKT, नग…)। बिल में न लिखी हो तो "Nos" लिखो।
6. total = आख़िरी देय रकम (GST और राउंड-ऑफ़ के बाद वाली TOTAL पंक्ति),
   subTotal नहीं।
7. सब रकम सादे अंक: ₹ नहीं, कॉमा नहीं। "1,665.0" → 1665
8. तारीख़ भारतीय ढंग की है — DD-MM-YYYY। "27-07-2026" → "2026-07-27"
9. paidVia = CASH / UPI / CARD / CREDIT, अगर बिल पर लिखा हो।
10. सामान के नाम जैसे छपे हैं वैसे ही रखो — हिंदी हो तो हिंदी में। न
    अनुवाद करो, न "सही" वर्तनी बनाओ।
11. summary = एक छोटी हिंदी पंक्ति: दुकान का नाम और क्या ख़रीदा।
12. जो सचमुच पढ़ा न जाए उसे null रखो — अंदाज़े से कोई अंक मत भरो।

सिर्फ़ JSON लौटाओ।
```

---

## नमूना — जवाब ऐसा दिखेगा

```json
{
  "party": { "name": "J M MOTEL & RESORT", "phone": "9828822300",
             "gstin": "08AKAPK9348K1ZQ", "address": "BARMER" },
  "invoiceNo": "12081",
  "billDate": "2026-07-27",
  "items": [
    { "name": "MINRAL WATER",   "qty": 1,  "unit": "Nos", "rate": 20,  "amount": 20 },
    { "name": "MUTTER PANEER",  "qty": 1,  "unit": "Nos", "rate": 210, "amount": 210 },
    { "name": "BUTTER TANDOORI ROTI", "qty": 12, "unit": "Nos", "rate": 18, "amount": 216 }
  ],
  "subTotal": 1586, "gst": 79.3, "roundOff": -0.3, "total": 1665,
  "paidVia": "CASH",
  "summary": "J M Motel से खाना — ₹1665"
}
```

इसे कॉपी करके app में पेस्ट कर दें। पार्टी का बिज़नेस कार्ड भी इसी से अपने-आप बन जाएगा।
