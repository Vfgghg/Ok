window.onload = function () {
    // URLs of the images
    var logoUrl = "https://cors-anywhere.herokuapp.com/https://freepngimg.com/download/adidas/58136-logo-brand-adidas-sports-free-transparent-image-hd.png";
    var stampUrl = "https://cors-anywhere.herokuapp.com/https://www.onlygfx.com/wp-content/uploads/2017/12/ok-stamp-2-1024x1024.png";

    // Fetch and load the images
    fetchImage(logoUrl, 'logoImage');
    fetchImage(stampUrl, 'stampImage');
};

// Fetch and display image, also convert it to Base64
function fetchImage(imageUrl, imgId) {
    fetch(imageUrl)
        .then(response => response.blob())  // Get the blob of the image
        .then(blob => {
            // Convert blob to Base64 string using FileReader
            var reader = new FileReader();
            reader.onloadend = function () {
                var imgElement = document.getElementById(imgId);
                imgElement.src = reader.result; // Set Base64 as the image source
            };
            reader.readAsDataURL(blob); // This converts the blob into a Base64-encoded string
        })
        .catch(error => {
            console.error("Error fetching image:", error);
        });
}

// Function to download the PDF
window.jsPDF = window.jspdf.jsPDF;
function download11() {
    var doc = new jsPDF({
        orientation: "p",
        unit: "mm",
        format: "a4"
    });

    var invoiceElement = document.getElementById("report11");

    // Use html2canvas to capture the entire invoice element as an image
    html2canvas(invoiceElement, {
        scale: 2,
        useCORS: true
    }).then(canvas => {
        var imgData = canvas.toDataURL("image/png");
        var imgWidth = 190; // Image width
        var pageHeight = 297; // Page height in mm (A4 size)
        var imgHeight = (canvas.height * imgWidth) / canvas.width;

        // Add the image to the PDF
        doc.addImage(imgData, "PNG", 10, 10, imgWidth, imgHeight);

        // Generate the PDF and open it in a new tab
        var pdfData = doc.output("blob");
        var pdfUrl = URL.createObjectURL(pdfData);
        window.open(pdfUrl, "_blank");
    }).catch(error => {
        console.error("Error generating PDF:", error);
    });
}

---------------------------------------------------------------------
window.onload = function () {
    // Get the URLs of images dynamically by id
    var logoUrl = document.getElementById("logoUrl").value;   // Get URL from hidden input or similar
    var stampUrl = document.getElementById("stampUrl").value; // Get URL from hidden input or similar

    // Fetch and convert the images to Base64
    fetchAndConvertToBase64(logoUrl, 'logoImage');
    fetchAndConvertToBase64(stampUrl, 'stampImage');
};

// Fetch the image by URL and convert it to Base64
function fetchAndConvertToBase64(imageUrl, imgId) {
    // CORS Proxy to bypass the CORS issue
    var proxyUrl = "https://cors-anywhere.herokuapp.com/";

    // Fetch the image using the proxy to avoid CORS issues
    fetch(proxyUrl + imageUrl)
        .then(response => response.blob()) // Get the image blob
        .then(blob => {
            // Create a FileReader to convert blob to Base64
            var reader = new FileReader();
            reader.onloadend = function () {
                var base64String = reader.result.split(',')[1]; // Get the Base64 string (remove the prefix)

                console.log("Base64 string: ", base64String); // Optionally log the Base64 string for inspection

                // Set the Base64 string as the source for the image
                var imgElement = document.getElementById(imgId);
                imgElement.src = reader.result; // Set the image source as Base64
            };

            // Read the image blob as Base64
            reader.readAsDataURL(blob);
        })
        .catch(error => {
            console.error("Error fetching image:", error);
        });
}
window.jsPDF = window.jspdf.jsPDF;
function download11() {
    var doc = new jsPDF();

    // Capture the content of the invoice
    html2canvas(document.getElementById("report11")).then(function (canvas) {
        // Convert canvas to image data URL
        var imgData = canvas.toDataURL('image/png');

        // Add the image to the PDF
        doc.addImage(imgData, 'PNG', 10, 10, 180, 160);

        // Create a Blob object from the PDF and open it in a new tab
        var pdfBlob = doc.output('blob');
        var pdfUrl = URL.createObjectURL(pdfBlob);

        // Open the PDF in a new tab
        window.open(pdfUrl, '_blank');
    });
}
----------------------------------------------------------------------
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Invoice</title>

    <!-- Include Required Libraries -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://html2canvas.hertzen.com/dist/html2canvas.min.js"></script>

    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 20px;
            background-color: #f4f4f4;
        }

        .invoice {
            background: #fff;
            border: 1px solid #ccc;
            padding: 20px;
            width: 100%;
            max-width: 800px;
            margin: auto;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        }

        .header {
            text-align: center;
            margin-bottom: 20px;
        }

            .header img {
                max-width: 150px;
            }

        .items table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 10px;
        }

        .items th, .items td {
            border: 1px solid #000;
            padding: 8px;
            text-align: left;
        }

        .total {
            margin-top: 20px;
            text-align: right;
            font-weight: bold;
        }

        .download-link {
            display: block;
            text-align: center;
            margin-top: 20px;
        }

        /* Small Image */
        .small-image {
            width: 80px;
            height: 80px;
            object-fit: contain;
            display: block;
            margin: 10px auto;
        }
    </style>
</head>
<body>
    <div class="invoice" id="report11">
        <div class="header">
            <!-- Logo Image -->
            <img id="logoImage" src="https://freepngimg.com/download/adidas/58136-logo-brand-adidas-sports-free-transparent-image-hd.png" alt="Company Logo">
            <h1>Invoice</h1>
        </div>

        <div class="details">
            <p><strong>Invoice Number:</strong> INV-001</p>
            <p><strong>Date:</strong> 01/10/2023</p>
            <p><strong>Customer:</strong> John Doe</p>
        </div>

        <div class="items">
            <table>
                <thead>
                    <tr>
                        <th>Description</th>
                        <th>Quantity</th>
                        <th>Unit Price</th>
                        <th>Total</th>
                    </tr>
                </thead>
                <tbody>
                    <tr>
                        <td>Product 1</td>
                        <td>2</td>
                        <td>$50.00</td>
                        <td>$100.00</td>
                    </tr>
                    <tr>
                        <td>Product 2</td>
                        <td>1</td>
                        <td>$75.00</td>
                        <td>$75.00</td>
                    </tr>
                </tbody>
            </table>
        </div>

        <div class="total">
            <h3>Total: $175.00</h3>
        </div>

        <!-- Stamp Image -->
        <img id="stampImage" src="https://www.onlygfx.com/wp-content/uploads/2017/12/ok-stamp-2-1024x1024.png" alt="Stamp Logo" class="small-image">
    </div>

    <div class="download-link">
        <a href="javascript:void(0);" onclick="download11()" class="btn">Download PDF</a>
    </div>

    <!-- Link External JavaScript File -->
    <script src="~/Content/Report.js"></script>
    <input type="hidden" id="logoUrl" value="https://freepngimg.com/download/adidas/58136-logo-brand-adidas-sports-free-transparent-image-hd.png">
    <input type="hidden" id="stampUrl" value="https://www.onlygfx.com/wp-content/uploads/2017/12/ok-stamp-2-1024x1024.png">

</body>
</html>

