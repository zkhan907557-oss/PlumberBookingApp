                <div class="service-options">
                    ${optionsHtml}
                </div>
               
                <button class="btn btn-primary" id="toProblemStep" style="margin-top: 30px;" disabled>Continue</button>
            `;
           
            // Add event listeners to service options
            const serviceOptionsEl = document.querySelectorAll('.service-option');
            serviceOptionsEl.forEach(option => {
                option.addEventListener('click', () => {
                    serviceOptionsEl.forEach(o => o.classList.remove('selected'));
                    option.classList.add('selected');
                    currentBooking.specificService = option.getAttribute('data-id');
                    document.getElementById('toProblemStep').disabled = false;
                });
            });
           
            document.getElementById('toProblemStep').addEventListener('click', showProblemStep);
        }
       
        // Show problem description step
        function showProblemStep() {
            modalBody.innerHTML = `
                <div class="step-indicator">
                    <div class="step completed">1</div>
                    <div class="step active">2</div>
                    <div class="step">3</div>
                    <div class="step">4</div>
                </div>
               
                <h3 style="margin-bottom: 20px;">Describe Your Problem</h3>
               
                <div class="form-group">
                    <label>Please describe the issue in detail</label>
                    <textarea class="form-control" placeholder="E.g., My kitchen sink is clogged and water is not draining properly..." id="problemDescription"></textarea>
                </div>
               
                <div class="form-group">
                    <label>Upload photos (optional)</label>
                    <div class="upload-btn">
                        <i class="fas fa-camera"></i>
                        <span>Add Photos</span>
                    </div>
                </div>
               
                <button class="btn btn-primary" id="toPlumberStep" style="margin-top: 30px;">Continue</button>
            `;
           
            document.getElementById('toPlumberStep').addEventListener('click', () => {
                currentBooking.problem = document.getElementById('problemDescription').value;
                showPlumberSelectionStep();
            });
        }
       
        // Show plumber selection step
        function showPlumberSelectionStep() {
            // Filter plumbers by specialty
            const availablePlumbers = plumbers.filter(plumber =>
                plumber.specialty.includes(currentBooking.service.charAt(0).toUpperCase() + currentBooking.service.slice(1))
            );
           
            let plumbersHtml = '';
            availablePlumbers.forEach(plumber => {
                const specialties = plumber.specialty.map(s => {
                    let className = '';
                    if (s === 'Plumbing') className = 'tag-plumbing';
                    if (s === 'Motor') className = 'tag-motor';
                    if (s === 'Leakage') className = 'tag-leakage';
                    return `<span class="service-tag ${className}">${s}</span>`;
                }).join('');
               
                plumbersHtml += `
                    <div class="plumber-card" data-id="${plumber.id}">
                        <div class="plumber-avatar">
                            <img src="${plumber.avatar}" alt="${plumber.name}">
                        </div>
                        <div class="plumber-info">
                            <h3>${plumber.name}</h3>
                            <div class="plumber-rating">
                                <i class="fas fa-star"></i>
                                <i class="fas fa-star"></i>
                                <i class="fas fa-star"></i>
                                <i class="fas fa-star"></i>
                                <i class="fas fa-star-half-alt"></i>
                                <span>${plumber.rating} (${plumber.reviews})</span>
                            </div>
                            <div class="plumber-specialty">
                                ${specialties}
                            </div>
                        </div>
                        <div class="plumber-eta">${plumber.eta}</div>
                    </div>
                `;
            });
           
            modalBody.innerHTML = `
                <div class="step-indicator">
                    <div class="step completed">1</div>
                    <div class="step completed">2</div>
                    <div class="step active">3</div>
                    <div class="step">4</div>
                </div>
               
                <h3 style="margin-bottom: 20px;">Select a Plumber</h3>
               
                <div class="plumbers-list">
                    ${plumbersHtml}
                </div>
               
                <button class="btn btn-primary" id="toDateTimeStep" style="margin-top: 30px;" disabled>Continue</button>
            `;
           
            // Add event listeners to plumber cards
            const plumberCards = document.querySelectorAll('.plumber-card');
            plumberCards.forEach(card => {
                card.addEventListener('click', () => {
                    plumberCards.forEach(c => c.classList.remove('selected'));
                    card.classList.add('selected');
                    currentBooking.plumber = card.getAttribute('data-id');
                    document.getElementById('toDateTimeStep').disabled = false;
                });
            });
           
            document.getElementById('toDateTimeStep').addEventListener('click', showDateTimeStep);
        }
       
        // Show date/time selection step
        function showDateTimeStep() {
            const now = new Date();
            const defaultDateTime = new Date(now.getTime() + 60 * 60 * 1000); // 1 hour from now
            const formattedDateTime = defaultDateTime.toISOString().slice(0, 16);
           
            modalBody.innerHTML = `
                <div class="step-indicator">
                    <div class="step completed">1</div>
                    <div class="step completed">2</div>
                    <div class="step completed">3</div>
                    <div class="step active">4</div>
                </div>
               
                <h3 style="margin-bottom: 20px;">Select Time</h3>
               
                <div class="form-group">
                    <label>When do you need the service?</label>
                    <div style="display: flex; gap: 10px; margin-top: 10px;">
                        <button class="btn" id="btnNow" style="flex: 1;">Now</button>
                        <button class="btn" id="btnLater" style="flex: 1; background: var(--light-gray);">Later</button>
                    </div>
                </div>
               
                <div class="form-group" id="datetimeGroup">
                    <label>Select Date & Time</label>
                    <input type="datetime-local" class="form-control" id="bookingDateTime" value="${formattedDateTime}">
                </div>
               
                <button class="btn btn-primary" id="confirmBooking" style="margin-top: 30px;">Confirm Booking</button>
            `;
           
            // Set up now/later buttons
            const btnNow = document.getElementById('btnNow');
            const btnLater = document.getElementById('btnLater');
            const datetimeGroup = document.getElementById('datetimeGroup');
           
            btnNow.addEventListener('click', () => {
                btnNow.style.background = 'var(--primary)';
                btnNow.style.color = 'white';
                btnLater.style.background = 'var(--light-gray)';
                btnLater.style.color = 'var(--dark)';
                datetimeGroup.style.display = 'none';
                currentBooking.datetime = 'now';
            });
           
            btnLater.addEventListener('click', () => {
                btnLater.style.background = 'var(--primary)';
                btnLater.style.color = 'white';
                btnNow.style.background = 'var(--light-gray)';
                btnNow.style.color = 'var(--dark)';
                datetimeGroup.style.display = 'block';
                currentBooking.datetime = document.getElementById('bookingDateTime').value;
            });
           
            // Set initial state
            btnNow.click();
           
            document.getElementById('bookingDateTime').addEventListener('change', function() {
                currentBooking.datetime = this.value;
            });
           
            document.getElementById('confirmBooking').addEventListener('click', showConfirmation);
        }
       
        // Show confirmation step
        function showConfirmation() {
            const selectedPlumber = plumbers.find(p => p.id == currentBooking.plumber);
            const serviceName = serviceOptions[currentBooking.service].find(s => s.id == currentBooking.specificService).name;
           
            modalBody.innerHTML = `
                <div class="step-indicator">
                    <div class="step completed">1</div>
                    <div class="step completed">2</div>
                    <div class="step completed">3</div>
                    <div class="step completed">4</div>
                </div>
               
                <h3 style="margin-bottom: 20px; text-align: center;">Booking Confirmation</h3>
               
                <div class="plumber-details-large">
                    <div class="plumber-avatar-large">
                        <img src="${selectedPlumber.avatar}" alt="${selectedPlumber.name}">
                    </div>
                    <h3>${selectedPlumber.name}</h3>
                    <div class="plumber-rating">
                        <i class="fas fa-star"></i>
                        <i class="fas fa-star"></i>
                        <i class="fas fa-star"></i>
                        <i class="fas fa-star"></i>
                        <i class="fas fa-star-half-alt"></i>
                        <span>${selectedPlumber.rating} (${selectedPlumber.reviews} reviews)</span>
                    </div>
                    <div class="plumber-eta" style="margin-top: 10px;">ETA: ${selectedPlumber.eta}</div>
                </div>
               
                <div class="summary-item">
                    <div class="label">Service</div>
                    <div class="value">${serviceName}</div>
                </div>
               
                <div class="summary-item">
                    <div class="label">Date & Time</div>
                    <div class="value">${currentBooking.datetime === 'now' ? 'ASAP' : new Date(currentBooking.datetime).toLocaleString()}</div>
                </div>
               
                <div class="summary-item">
                    <div class="label">Rate</div>
                    <div class="value">${selectedPlumber.price}</div>
                </div>
               
                <div class="plumber-actions">
                    <button class="btn-call"><i class="fas fa-phone"></i> Call</button>
                    <button class="btn-message"><i class="fas fa-comment"></i> Message</button>
                </div>
               
                <button class="btn btn-primary" style="margin-top: 30px;" id="finishBooking">Confirm & Book</button>
            `;
           
            document.getElementById('finishBooking').addEventListener('click', () => {
                alert('Booking confirmed! Your plumber is on the way.');
                bookingModal.style.display = 'none';
                resetBooking();
            });
        }
       
        // Reset booking state
        function resetBooking() {
            currentBooking = {
                service: null,
                specificService: null,
                problem: "",
                location: "",
                datetime: null,
                plumber: null
            };
        }
    </script>
</body>
</html>
