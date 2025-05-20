# sales-data-form
<!DOCTYPE html>
<html>
  <head>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bulma@0.9.4/css/bulma.min.css" />
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" />
  </head>
  <body>
    <section class="hero is-primary is-bold">
      <div class="hero-body">
        <div class="container">
          <h1 class="title">Sales Data Entry Form</h1>
        </div>
      </div>
    </section>
    <form id="form" class="container m-4 pl-4">
      <!-- Employee Name (Required) -->
      <div class="field">
        <label class="label">Employee Name</label>
        <div class="control">
          <input
            class="input"
            type="text"
            placeholder="Your Name"
            name="Employee Name"
            id="employee-name"
            required
          />
        </div>
        <p class="help is-danger" id="employee-name-error" style="display: none;">Please enter your name</p>
      </div>

      <!-- Date & Time (Required) -->
      <div class="field">
        <label class="label">Date & Time of Sales</label>
        <div class="control">
          <input
            class="input"
            type="datetime-local" 
            name="Date Time"
            id="date-time"
            required
          />
        </div>
        <p class="help is-danger" id="date-time-error" style="display: none;">Please select a date and time</p>
      </div>

      <!-- Vendor (Required) -->
      <div class="field">
        <label class="label">Vendor</label>
        <div class="control">
          <input
            class="input"
            type="text"
            placeholder="Vendor Name"
            name="Vendor Name"
            id="vendor-name"
            required
          />
        </div>
        <p class="help is-danger" id="vendor-error" style="display: none;">Please enter the vendor name</p>
      </div> 

      <!-- Vendor Address (Required) -->
      <div class="field">
        <label class="label">Vendor Address</label>
        <div class="control">
          <input
            class="input"
            type="text"
            placeholder="Vendor Address"
            name="Vendor Address"
            id="vendor-address"
            required
          />
        </div>
        <p class="help is-danger" id="vendor-address-error" style="display: none;">Please enter the vendor address</p>
      </div>

      <!-- Item (Required) -->
      <div class="field">
        <label class="label">Items</label>
        <div id="items-container">
          <!-- First item row -->
          <div class="field is-grouped item-row">
            <div class="control is-expanded">
              <div class="select is-fullwidth">
                <select class="input item-select" name="Item[]" required>
                  <option value="" disabled selected>Select an item</option>
                  <option value="Royal Canin">Royal Canin</option>
                  <option value="Whiskas">Whiskas</option>
                  <option value="Cat Choize">Cat Choize</option>
                  <option value="Other">Other (please specify)</option>
                </select>
              </div>
            </div>
            <div class="control other-item-container" style="display: none; margin-left: 10px; flex-grow: 1;">
              <input class="input other-item-input" type="text" placeholder="Specify item" name="OtherItem[]">
            </div>
            <div class="control" style="width: 120px; margin-left: 10px;">
              <input class="input qty-input" type="number" placeholder="Qty" name="Qty[]" min="1" required>
            </div>
            <div class="control">
              <button type="button" class="button is-danger remove-item" style="display: none;">×</button>
            </div>
          </div>
        </div>
        <p class="help is-danger" id="items-error" style="display: none;">Please add at least one item</p>
        <button type="button" id="add-item" class="button is-small is-link mt-2">+ Add Another Item</button>
      </div>

      <!-- Receipt Upload (Optional) -->
      <div class="field">
        <label class="label">Receipt Photo</label>
        <div class="control">
          <div class="file has-name">
            <label class="file-label">
              <input class="file-input" type="file" id="receipt" name="receipt" accept="image/*">
              <span class="file-cta">
                <span class="file-icon">
                  <i class="fas fa-upload"></i>
                </span>
                <span class="file-label">
                  Choose a file…
                </span>
              </span>
              <span class="file-name" id="receipt-name">
                No file selected
              </span>
            </label>
          </div>
        </div>
        <p class="help">Optional: Upload a photo of the receipt (max 5MB)</p>
      </div>

      <!-- Additional Information (Optional) -->
      <div class="field">
        <label class="label">Additional Information</label>
        <div class="control">
          <textarea
            class="textarea"
            placeholder="Any additional information"
            name="Notes"
            id="notes"
          ></textarea>
        </div>
      </div>

      <div class="field is-grouped">
        <div class="control">
          <button class="button is-primary" type="submit" id="submit-button">
            Submit Data
          </button>
        </div>
      </div>
    </form>

    <!-- Success/Error Message -->
    <div id="message" class="container" style="display: none; margin: 20px; font-weight: bold; padding: 8px; border-radius: 4px;"></div>

    <script>
      document.addEventListener('DOMContentLoaded', function() {
        // File upload name display
        const fileInput = document.querySelector('#receipt');
        fileInput.addEventListener('change', (event) => {
          const fileName = event.target.files[0] ? event.target.files[0].name : 'No file selected';
          document.querySelector('#receipt-name').textContent = fileName;
        });

        // Add item row functionality
        document.getElementById('add-item').addEventListener('click', addItemRow);

        // Item select change handler
        document.addEventListener('change', function(e) {
          if (e.target.classList.contains('item-select')) {
            const row = e.target.closest('.item-row');
            const otherContainer = row.querySelector('.other-item-container');
            if (e.target.value === 'Other') {
              otherContainer.style.display = 'block';
            } else {
              otherContainer.style.display = 'none';
            }
          }
        });

        // Remove item button handler
        document.addEventListener('click', function(e) {
          if (e.target.classList.contains('remove-item')) {
            const row = e.target.closest('.item-row');
            row.remove();
          }
        });

        function addItemRow() {
          const container = document.getElementById('items-container');
          const newRow = container.querySelector('.item-row').cloneNode(true);
          
          // Clear values in the new row
          newRow.querySelector('.item-select').value = '';
          newRow.querySelector('.other-item-input').value = '';
          newRow.querySelector('.qty-input').value = '';
          newRow.querySelector('.other-item-container').style.display = 'none';
          
          // Show remove button (except for first row)
          newRow.querySelector('.remove-item').style.display = 'block';
          
          container.appendChild(newRow);
        }

        // Form submission
        document.getElementById("form").addEventListener("submit", async function (e) {
          e.preventDefault();
          
          // Validate form
          let isValid = true;
          
          // Employee name validation
          const employeeName = document.getElementById('employee-name').value.trim();
          if (!employeeName) {
            document.getElementById('employee-name-error').style.display = 'block';
            isValid = false;
          } else {
            document.getElementById('employee-name-error').style.display = 'none';
          }
          
          // Date time validation
          const dateTime = document.getElementById('date-time').value;
          if (!dateTime) {
            document.getElementById('date-time-error').style.display = 'block';
            isValid = false;
          } else {
            document.getElementById('date-time-error').style.display = 'none';
          }
          
          // Vendor name validation
          const vendorName = document.getElementById('vendor-name').value.trim();
          if (!vendorName) {
            document.getElementById('vendor-error').style.display = 'block';
            isValid = false;
          } else {
            document.getElementById('vendor-error').style.display = 'none';
          }
          
          // Vendor address validation
          const vendorAddress = document.getElementById('vendor-address').value.trim();
          if (!vendorAddress) {
            document.getElementById('vendor-address-error').style.display = 'block';
            isValid = false;
          } else {
            document.getElementById('vendor-address-error').style.display = 'none';
          }
          
          // Items validation
          const itemRows = document.querySelectorAll('.item-row');
          if (itemRows.length === 0) {
            document.getElementById('items-error').style.display = 'block';
            isValid = false;
          } else {
            document.getElementById('items-error').style.display = 'none';
            
            // Validate each item row
            itemRows.forEach(row => {
              const select = row.querySelector('.item-select');
              const qty = row.querySelector('.qty-input').value;
              
              if (!select.value) {
                isValid = false;
                select.classList.add('is-danger');
              } else {
                select.classList.remove('is-danger');
              }
              
              if (select.value === 'Other' && !row.querySelector('.other-item-input').value.trim()) {
                isValid = false;
                row.querySelector('.other-item-input').classList.add('is-danger');
              } else {
                row.querySelector('.other-item-input').classList.remove('is-danger');
              }
              
              if (!qty) {
                isValid = false;
                row.querySelector('.qty-input').classList.add('is-danger');
              } else {
                row.querySelector('.qty-input').classList.remove('is-danger');
              }
            });
          }
          
          if (!isValid) {
            return;
          }
          
          // Process items data
          const itemsData = [];
          itemRows.forEach(row => {
            const select = row.querySelector('.item-select');
            const qty = row.querySelector('.qty-input').value;
            let itemName = select.value;
            
            if (itemName === 'Other') {
              itemName = row.querySelector('.other-item-input').value.trim();
            }
            
            itemsData.push({
              item: itemName,
              qty: qty
            });
          });
          
          // Prepare form data as JSON
          const formData = {
            employeeName: employeeName,
            dateTime: dateTime,
            vendorName: vendorName,
            vendorAddress: vendorAddress,
            notes: document.getElementById('notes').value,
            items: itemsData
          };
          
          // Handle file upload if present
          const receiptFile = document.getElementById('receipt').files[0];
          if (receiptFile) {
            if (receiptFile.size > 5 * 1024 * 1024) {
              showMessage("Receipt image must be less than 5MB", "error");
              return;
            }
            
            // Convert file to base64 for JSON transmission
            formData.receipt = {
              name: receiptFile.name,
              mimeType: receiptFile.type,
              data: await toBase64(receiptFile)
            };
          }
          
          // Submit data
          try {
            await submitData(formData);
          } catch (error) {
            console.error("Submission error:", error);
            showMessage("Error: " + error.message, "error");
          }
        });
        
        // Helper function to convert file to base64
        function toBase64(file) {
          return new Promise((resolve, reject) => {
            const reader = new FileReader();
            reader.readAsDataURL(file);
            reader.onload = () => resolve(reader.result.split(',')[1]);
            reader.onerror = error => reject(error);
          });
        }
        
        // Helper function to show messages
        function showMessage(message, type) {
          const messageEl = document.getElementById("message");
          messageEl.textContent = message;
          messageEl.style.display = "block";
          
          if (type === "success") {
            messageEl.style.backgroundColor = "#e8f5e9";
            messageEl.style.color = "#2e7d32";
          } else {
            messageEl.style.backgroundColor = "#ffebee";
            messageEl.style.color = "#d32f2f";
          }
          
          setTimeout(() => {
            messageEl.style.display = "none";
          }, 5000);
        }
        
        async function submitData(data) {
          const messageEl = document.getElementById("message");
          const submitBtn = document.getElementById("submit-button");
          
          showMessage("Submitting...", "success");
          submitBtn.disabled = true;
          
          try {
            const scriptUrl = "https://script.google.com/macros/s/AKfycbztsDrBzMvj4eHrKtr31GigdkMMEXVZbiQX-wrbVI5AI-U710cyZHx3AtMiApBR9uM7Zg/exec";
            const noCacheUrl = `${scriptUrl}?nocache=${Date.now()}`;
            
            const response = await fetch(noCacheUrl, {
              method: "POST",
              headers: {
                "Content-Type": "application/json",
              },
              body: JSON.stringify(data),
              redirect: 'follow'
            });
            
            const text = await response.text();
            let jsonResponse;
            
            try {
              jsonResponse = JSON.parse(text);
            } catch (e) {
              const match = text.match(/<script.*?>([\s\S]*?)<\/script>/);
              if (match && match[1]) {
                jsonResponse = JSON.parse(match[1].trim());
              } else {
                throw new Error("Unexpected response format");
              }
            }
            
            if (jsonResponse.status === "error") {
              throw new Error(jsonResponse.message);
            }
            
            showMessage("Success! " + jsonResponse.message, "success");
            document.getElementById("form").reset();
            document.getElementById("receipt-name").textContent = "No file selected";
          } catch (error) {
            console.error("Error:", error);
            showMessage("Error: " + (error.message || "Failed to submit data"), "error");
            throw error;
          } finally {
            submitBtn.disabled = false;
          }
        }
      });
    </script>
  </body>
</html>
