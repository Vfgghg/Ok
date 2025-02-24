
To implement the **PDF download** feature using an `<a>` tag with `onclick="download()"` and no inline scripts in your `.cshtml`, you'll need the **`html2pdf.js`** library.

---

## ✅ **1. Installing `html2pdf.js` Library**

You can install **`html2pdf.js`** in two ways:

### **Option 1: Use CDN (Quick & Easy)**
Add this in your **`_Layout.cshtml`** inside the `<head>` or at the end before `</body>`:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js"></script>
```

---

### **Option 2: Install via NuGet or NPM (For Larger Projects)**
If you're managing front-end packages with **NPM**:

```bash
npm install html2pdf.js
```

Or using **NuGet** (if set up for front-end libraries):

```bash
Install-Package html2pdf.js
```

For most ASP.NET MVC projects, **CDN** is the simplest.

---

## ✅ **2. HTML Setup in `InvoiceView.cshtml`**

```html
@{
    ViewBag.Title = "Invoice";
    Layout = "~/Views/Shared/_Layout.cshtml";
}

<div id="invoiceContent">
    <h2>Invoice #001</h2>
    <p>Date: 24/02/2025</p>
    <p>Customer: John Doe</p>

    <table border="1" width="100%" cellpadding="5">
        <tr>
            <th>Item</th>
            <th>Qty</th>
            <th>Price</th>
            <th>Total</th>
        </tr>
        <tr>
            <td>Product A</td>
            <td>2</td>
            <td>$50</td>
            <td>$100</td>
        </tr>
        <tr>
            <td>Product B</td>
            <td>1</td>
            <td>$75</td>
            <td>$75</td>
        </tr>
    </table>

    <h3>Total: $175</h3>
</div>

<!-- Download Link -->
<a href="javascript:void(0);" onclick="download()" class="btn btn-primary">Download as PDF</a>

<!-- External JS File -->
<script src="~/js/invoice.js"></script>
```

---

## ✅ **3. JavaScript in `invoice.js`**

```javascript
function download() {
    // Target the invoice content
    const element = document.getElementById("invoiceContent");

    // PDF generation options
    const opt = {
        margin: 10,
        filename: 'Invoice.pdf', // Static filename, can be made dynamic
        image: { type: 'jpeg', quality: 0.98 },
        html2canvas: { scale: 2 },
        jsPDF: { unit: 'mm', format: 'a4', orientation: 'portrait' }
    };

    // Generate and download the PDF
    html2pdf().set(opt).from(element).save();
}
```

---

## ✅ **4. How It Works:**
1. **User clicks** the `<a>` tag with `onclick="download()"`.
2. The **`download()`** function in `invoice.js` targets the `#invoiceContent`.
3. **`html2pdf.js`** converts the HTML to PDF and triggers the download.
4. The PDF is saved as **`Invoice.pdf`**.

---

## ⚡ **Additional Customizations:**
- **Dynamic Filename:**  
  Change this line in `invoice.js`:

  ```javascript
  filename: 'Invoice_' + new Date().getTime() + '.pdf',
  ```

  This adds a timestamp to the filename.

- **Multi-Page Support:**  
  If your invoice might be long, use this in options:

  ```javascript
  html2canvas: { scale: 2, scrollY: 0 }
  ```

- **Exclude Elements from PDF:**  
  Add this CSS to hide certain elements (like download button):

  ```css
  @media print {
      .btn-primary { display: none; }
  }
  ```

---

Let me know if you want help with **multi-page invoices**, **adding watermarks**, or **customizing styles** in the PDF!

Got it! Since your **`.cshtml` file has a `@model`** and you're using a **layout**, let's integrate the **PDF download** while still following your requirements:

1. **Using a model in the `.cshtml`**  
2. **Maintaining the layout**  
3. **No inline `<script>` tags**  
4. **Using an `<a>` tag with `onclick="download()"`**  

---

## ✅ **1. Complete `InvoiceView.cshtml` (With `@model`)**

```html
@model YourNamespace.Models.InvoiceModel
@{
    ViewBag.Title = "Invoice";
    Layout = "~/Views/Shared/_Layout.cshtml";
}

<div id="invoiceContent">
    <h2>Invoice #@Model.InvoiceNumber</h2>
    <p>Date: @Model.InvoiceDate.ToString("dd/MM/yyyy")</p>
    <p>Customer: @Model.CustomerName</p>

    <table border="1" width="100%" cellpadding="5">
        <tr>
            <th>Item</th>
            <th>Qty</th>
            <th>Price</th>
            <th>Total</th>
        </tr>
        @foreach (var item in Model.Items)
        {
            <tr>
                <td>@item.Name</td>
                <td>@item.Quantity</td>
                <td>@item.Price.ToString("C")</td>
                <td>@(item.Quantity * item.Price).ToString("C")</td>
            </tr>
        }
    </table>

    <h3>Total: @Model.TotalAmount.ToString("C")</h3>
</div>

<!-- Download Link -->
<a href="javascript:void(0);" onclick="download()" class="btn btn-primary">Download as PDF</a>

<!-- Load External JS & html2pdf.js CDN -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js"></script>
<script src="~/js/invoice.js"></script>
```

---

## ✅ **2. The Model Example (`InvoiceModel.cs`)**

```csharp
public class InvoiceModel
{
    public string InvoiceNumber { get; set; }
    public DateTime InvoiceDate { get; set; }
    public string CustomerName { get; set; }
    public List<InvoiceItem> Items { get; set; }
    public decimal TotalAmount => Items.Sum(item => item.Quantity * item.Price);
}

public class InvoiceItem
{
    public string Name { get; set; }
    public int Quantity { get; set; }
    public decimal Price { get; set; }
}
```

---

## ✅ **3. JavaScript (`invoice.js`) — External File**

```javascript
function download() {
    // Target the invoice content
    const element = document.getElementById("invoiceContent");

    // Generate PDF options
    const opt = {
        margin: 10,
        filename: 'Invoice.pdf', // You can make this dynamic if needed
        image: { type: 'jpeg', quality: 0.98 },
        html2canvas: { scale: 2 },
        jsPDF: { unit: 'mm', format: 'a4', orientation: 'portrait' }
    };

    // Convert HTML to PDF and trigger download
    html2pdf().set(opt).from(element).save();
}
```

---

## ✅ **4. Folder Structure Example:**

```
- Controllers/
  - InvoiceController.cs
- Models/
  - InvoiceModel.cs
- Views/
  - Invoice/
    - InvoiceView.cshtml
- wwwroot/
  - js/
    - invoice.js
```

---

## ⚡ **How It Works:**
1. The **`@model`** populates the invoice with data.
2. The **`<a>` tag** triggers the `download()` function.
3. The **external JS** uses `html2pdf.js` to generate and download the PDF.
4. The **layout remains intact** and no inline scripts are used.

---

💡 **To Make Filename Dynamic (Based on Model Data):**  
If you want the downloaded PDF name to be like `Invoice_12345.pdf`, modify `invoice.js` like this:

```javascript
function download() {
    const element = document.getElementById("invoiceContent");
    const invoiceNumber = document.querySelector("h2").innerText.split('#')[1]; // Extracting invoice number

    const opt = {
        margin: 10,
        filename: 'Invoice_' + invoiceNumber.trim() + '.pdf',
        image: { type: 'jpeg', quality: 0.98 },
        html2canvas: { scale: 2 },
        jsPDF: { unit: 'mm', format: 'a4', orientation: 'portrait' }
    };

    html2pdf().set(opt).from(element).save();
}
```

---

This approach **respects the layout**, **uses the model**, and **avoids inline scripts**, while giving you the full PDF download functionality. 

Let me know if you'd like help with **custom headers**, **watermarks**, or **multi-page invoices**!


  
