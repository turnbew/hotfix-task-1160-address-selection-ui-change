TASK DATE: 19.10.2017

TASK SHORT DESCRIPTION: 1160 ('address selection ui change' layout changing)

GITHUB REPOSITORY CODE: hotfix/task-1160-address-selection-ui-change

ORIGINAL WORK: https://github.com/BusinessBecause/network-site/tree/hotfix/task-1160-address-selection-ui-change

CHANGES

	IN FILES:
	
	\network-site\addons\default\modules\firesale\controllers\front_cart.php
	
		added code: 
		
			->append_css('module::checkout.css')
		
	
	\network-site\addons\default\modules\firesale\js\firesale.js
	
		added code: 
			
			
				/* ************************************************************* */
				/* ************************************************************* */
				/* ************************************************************* */
				/* 																							
				/*		CONTROL PART OF SHOP/CHEACKOUT/ADDRESS FIELDS
				/*
				/* ************************************************************* */
				/* ************************************************************* */
				/* ************************************************************* */

				//If one of the radio buttons changing in 'BILLING DETAILS' part
				$('.radio_bill_to').change(function() {
					clearInputFieldsData('.fields_container_bill_to');
					setLayoutChangesRadio('bill_to', $(this));
				})	
				
				
				
				
				
				//If 'My Billing and Shipping addresses are the same' checkbox changing
				$('#bill_details_same').change(function() {
					var checked = $(this).prop('checked');
					setLayoutChangesCheckbox('bill_to', checked);
					checked
						? copySelectedShippingAddress()
						: clearInputFieldsData('.fields_container_bill_to');
				})
				
				
				
				

				//If one of the radio buttons changing in 'SHIPPING DETAILS' part
				$('.radio_ship_to').change(function() {
					clearInputFieldsData('.fields_container_ship_to');
					setLayoutChangesRadio('ship_to', $(this));
					copySelectedShippingAddress();
				})
				
				
				
					
					
				//If 'use my profile data address' checkbox changing
				$('#ship_to_profile_address').change(function() {
					var checked = $(this).prop('checked');
					setButtonsDisabledProperty('ship_to', checked);
					setLayoutChangesCheckbox('ship_to', checked);
					checked
						? setProfileAddressData()
						: clearInputFieldsData('.fields_container_ship_to');
				})


				
				
				
				function setLayoutChangesCheckbox(selector, checked) {
					$('.radio_' + selector).prop('checked', false);
					$('.tr_' + selector).removeClass('active_' + selector).addClass('inactive');		
					if (!checked)  {
						$('.radio_' + selector + ':first').prop('checked', true);
						$('.tr_' + selector + ':first').addClass('active_' + selector).removeClass('inactive');
						$('.fields_container_' + selector).css('display', 'none');	
					} else {
						$('.fields_container_' + selector).css('display', 'block');	
						$('.fields_' + selector).addClass('active');
					}	
				}	
				
				
				
				
				
				function setLayoutChangesRadio(selector, obj) {
					$('.radio_' + selector).prop('checked', false);
					$('.tr_' + selector).removeClass('active_' + selector).addClass('inactive');
					obj.prop('checked', true);
					button_id = obj.prop('class').replace('radio_' + selector + ' ', '');
					$('.tr_' + selector + '.' + button_id).addClass('active_' + selector).removeClass('inactive');
					$('.fields_container_' + selector).css('display', 'none');	
					$('.checkbox_' + selector).prop('checked', false);
				}		
				
				
				
				
				
				function setButtonsDisabledProperty(selector, checked) {
					$('.radio_' + selector  + ', .checkbox_' + selector).prop('disabled', checked);
				}
				
				
				
				
				
				function setProfileAddressData() {
					$.getJSON(SITE_URL + 'network_settings/content/shop/orders/ajax_user_address/' + USER_ID, function(data) {
						if ( data != 'false' ) {
							for( var k in data ) { 
								$('#ship_' + k).val(data[k]); 
							}
						}
						setButtonsDisabledProperty('ship_to', false);
						copySelectedShippingAddress()
					});	
				}
				
				
				
				
				
				function clearInputFieldsData(selector) {
					$(selector).find('input').val('');
				}
				
				
				
				
				
				function copySelectedShippingAddress() {
					clearInputFieldsData('.fields_container_bill_to');
					var field_slugs = [
						'company',
						'firstname',
						'lastname',
						'email',
						'phone',
						'address1',
						'address2',
						'city',
						'county',
						'postcode',
						'country'		
					];
					if ($('#bill_details_same').prop('checked')) {
						var selector = '';
						if ($('#ship_to_profile_address').prop('checked')) {
							selector = '#ship_';
						} else {
							selector = $('input[name=ship_to]:checked').prop('class').replace('radio_ship_to ', '');
							selector = '#' + selector + '_';
						}
						if (selector != '') {
							field_slugs.forEach(function(field_slug) {
								if ($(selector + field_slug).length) {
									$('#bill_' + field_slug).val($(selector + field_slug).val());
								}
							});
						}
					}
				}
				
	
				/* ************************************************************* */
				/* 																							
				/*		END of CONTROL PART OF SHOP/CHEACKOUT/ADDRESS FIELDS
				/*
				/* ************************************************************* */
				
				
		\network-site\addons\default\modules\firesale\css\checkout.css
		
			added file and code
			
				table.checkout_addresses {
					margin-top: 10px;
					margin-right: 20px;
					width: 95%;
					margin-left: 0px !important;
				}
				table.checkout_addresses td {
					padding: 3px; 
				}
				table.checkout_addresses td.input {
					vertical-align: top;
					padding-top: 2px;
					width: 20px !important;
				}
				table.checkout_addresses td.input label{
					margin: 0px;
					padding: 0px;
				}
				table.checkout_addresses td.gap_small {
					min-height: 5px !important;
				}
				table.checkout_addresses td.gap_big {
					min-height: 30px !important;
				}
				table.checkout_addresses td.tag {
					padding-top: 5px !important;
				}
				td.address_ship_to,
				td.address_bill_to {
					padding-top: 6px;
					font-size: 14px;
					font-weight: bold;
					margin-top: 5px;
					border-bottom: solid 1px #ccc;
				}
				td.address_bill_to  {
					border-bottom: solid 1px #3998ff; 
				}
				.fields_container_ship_to,
				.fields_container_bill_to {
					display: none;
					width: 95%;
					margin: 0px;
					padding: 0px;		
				}
				tr.active_ship_to td.list,
				td.fields_ship_to div.fields_container_ship_to {
					background-color: #fafafa;
				}
				tr.active_bill_to td.list,
				td.fields_bill_to div.fields_container_bill_to {
					background-color: #f1f8ff;
				}
				tr.inactive td.list {
					background-color: #fff;
				}
				td.fields_ship_to input, 
				td.fields_ship_to select,
				td.fields_ship_to label,
				td.fields_bill_to input, 
				td.fields_bill_to select,
				td.fields_bill_to label {
					max-width: 80% !important;
					margin-left: 10%;
					margin-top: 4px;
					margin-bottom: 10px;
				}
				td.fields_ship_to label, 
				td.fields_bill_to label {
					margin-bottom: 0px;
				}
				
				
		\network-site\addons\default\modules\firesale\views\checkout.php
		
			changed code
			
				 <div class="col-sm-4"><!-- START SHIPPING DETAILS -->
                        <?php if( $ship_req ): ?>
						<h5><?php echo lang('firesale:title:ship'); ?></h5>                       
							<?php if( isset($addresses) && !empty($addresses) ): ?>
							<fieldset>
								<table class="checkout_addresses">
									<?php 
										$counter = 1;
										foreach( $addresses AS $key => $address): 
									?>
									<tr>
										<td class="input">
											<label for="ship_to_<?php echo $address['id']; ?>">
												<input class="radio_ship_to address<?php echo $counter  ?>" name="ship_to" id="ship_to_<?php echo $address['id']; ?>" type="radio" value="<?php echo $address['id']; ?>"<?php echo ( ( isset($_POST['ship_to']) && $_POST['ship_to'] == $address['id'] ) || $key == 0 ? ' checked="checked"' : '' ); ?> />
											</label>	
										</td>
										<td class="address_ship_to">
											<?php echo $address['title']; ?>
										</td>
									</tr>
									<tr>
										<td colspan="2" class="gap_small"></td>
									</tr>
									<tr class="tr_ship_to address<? echo $counter . (($counter == 1) ? ' active_ship_to' : ' incative') ?>">
										<td class="empty"></td>
										<td class="list">
											<ul>
												<?php echo ( isset($address['firstname']) ? '<li>' . $address['firstname'] . ' ' . $address['lastname'] . '</li>' : '' ); ?>
												<?php echo ( isset($address['address1']) ? '<li>' . $address['address1'] . '</li>' : '' ); ?>
												<?php echo ( isset($address['address2']) ? '<li>' . $address['address2'] . '</li>' : '' ); ?>
												<?php echo ( isset($address['city']) ? '<li>' . $address['city'] . '</li>' : '' ); ?>
												<?php echo ( isset($address['county']) ? '<li>' . $address['county'] . '</li>' : '' ); ?>
												<?php echo ( isset($address['postcode']) ? '<li>' . $address['postcode'] . '</li>' : '' ); ?>
												<?php echo ( isset($address['country']['name']) ? '<li>' . $address['country']['name'] . '</li>' : '' ); ?>
												<input type="hidden" id="address<? echo $counter?>_firstname" value="<?php echo $address['firstname'] ?>">
												<input type="hidden" id="address<? echo $counter?>_lastname" value="<?php echo $address['lastname'] ?>">
												<input type="hidden" id="address<? echo $counter?>_address1" value="<?php echo $address['address1'] ?>">
												<input type="hidden" id="address<? echo $counter?>_address2" value="<?php echo $address['address2'] ?>">
												<input type="hidden" id="address<? echo $counter?>_city" value="<?php echo $address['city'] ?>">
												<input type="hidden" id="address<? echo $counter?>_county" value="<?php echo $address['county'] ?>">
												<input type="hidden" id="address<? echo $counter?>_postcode" value="<?php echo $address['postcode'] ?>">
												<input type="hidden" id="address<? echo $counter?>_country" value="<?php echo $address['country'] ?>">
											</ul>
										</td>
									</tr>
									<tr><td class="gap_big" colspan="2">&nbsp;</td></tr>
									<?php
										$counter++;
										endforeach; 
									?>								
									<tr>
										<td class="input">
											<label for="ship_to_profile_address">
												<input class="checkbox_ship_to" name="ship_to_profile_address" id="ship_to_profile_address" type="checkbox"/>
											</label>										
										</td>
										<td class="tag">
											Use my profile address
										</td>
									</tr>
									<tr>
										<td class="input">
											<label for="ship_to_new">
												<input name="ship_to" id="ship_to_new" type="radio" value="new"<?php echo ( isset($_POST['ship_to']) && $_POST['ship_to'] == 'new' ? ' checked="checked"' : '' ); ?> />												
											</label>
										</td>
										<td class="tag">
											<?php echo lang('firesale:addresses:new_address'); ?>
										</td>
									</tr>
									<tr>
										<td colspan="2" class="fields_ship_to">
											<div class="fields_container_ship_to">
											<?php foreach( $ship_fields AS $subtitle => $section ): ?>
												<div<?php echo ( !isset($addresses) || empty($addresses) ? ' style="display: none"' : '' ); ?>>
												<?php foreach( $section AS $field ): ?>
													<div>
														<label for="<?php echo $field['input_slug']; ?>">
															<?php echo lang(substr($field['input_title'], 5)); ?> <?php echo $field['required']; ?>:
														</label>
														<div>
															<?php echo $field['input']; ?>
														</div>
														<?php echo ( form_error($field['input_slug']) ? '<div class="error">'.form_error($field['input_slug']).'</div>' : '' ); ?>
													</div>
												<?php endforeach; ?>
												</div>
											<?php endforeach; ?>
											</div><!-- END fields_container_ship_to -->
										</td>
									</tr>
								</table>
							</fieldset>
							<?php endif; ?>
						<?php endif; ?>
					</div><!-- END SHIPPING DETAILS -->
					
					
                    
                    <div class="col-sm-4"><!-- START BILLING DETAILS -->
                        <h5 class="bottom-buffer"><?php echo lang('firesale:title:bill'); ?></h5>
                        <?php if( isset($addresses) && !empty($addresses) ): ?>
						<fieldset>
							<table class="checkout_addresses">
								<?php 
										$counter = 1;
										foreach( $addresses AS $key => $address): 
									?>
								<tr>
									<td class="input">
										<label for="bill_to_<?php echo $address['id']; ?>">	
											<input class="radio_bill_to address<?php echo $counter  ?>" name="bill_to" id="bill_to_<?php echo $address['id']; ?>" type="radio" value="<?php echo $address['id']; ?>"<?php echo ( ( isset($_POST['ship_to']) && $_POST['ship_to'] == $address['id'] ) || $key == 0 ? ' checked="checked"' : '' ); ?> />
										</label>	
									</td>
									<td class="address_bill_to">
										<?php echo $address['title']; ?>
									</td>
								</tr>
								<tr>
									<td colspan="2" class="gap_small"></td>
								</tr>
								<tr class="tr_bill_to address<? echo $counter . (($counter == 1) ? ' active_bill_to' : ' incative') ?>">
									<td class="empty"></td>
									<td class="list">
										<ul>
											<?php echo ( isset($address['firstname']) ? '<li>' . $address['firstname'] . ' ' . $address['lastname'] . '</li>' : '' ); ?>
											<?php echo ( isset($address['address1']) ? '<li>' . $address['address1'] . '</li>' : '' ); ?>
											<?php echo ( isset($address['address2']) ? '<li>' . $address['address2'] . '</li>' : '' ); ?>
											<?php echo ( isset($address['city']) ? '<li>' . $address['city'] . '</li>' : '' ); ?>
											<?php echo ( isset($address['county']) ? '<li>' . $address['county'] . '</li>' : '' ); ?>
											<?php echo ( isset($address['postcode']) ? '<li>' . $address['postcode'] . '</li>' : '' ); ?>
											<?php echo ( isset($address['country']['name']) ? '<li>' . $address['country']['name'] . '</li>' : '' ); ?>
										</ul>
									</td>
								</tr>
								<tr>
									<td class="gap_big" colspan="2">&nbsp;</td>
								</tr>
								<?php 
									$counter++;
									endforeach; 
								?>	
								<tr>
									<td class="input">
										<label for="bill_details_same">
											<input class="checkbox_bill_to" type="checkbox" name="bill_details_same" id="bill_details_same" value="yes" />
										</label>
									</td>
									<td class="tag">
										My Billing and Shipping addresses are the same.
									</td>
								</tr>
								<tr>
									<td class="input">
										<label for="bill_to_new">
											<input name="bill_to" id="bill_to_new" type="radio" value="new"<?php echo ( isset($_POST['bill_to']) && $_POST['bill_to'] == 'new' ? ' checked="checked"' : '' ); ?> />
										</label>
									</td>
									<td class="tag"><?php echo lang('firesale:addresses:new_address'); ?></td>
								</tr>
								<tr>
									<td colspan="2" class="fields_bill_to">
										<div class="fields_container_bill_to">
										<?php foreach( $bill_fields AS $subtitle => $section ): ?>
											<div<?php echo ( !isset($addresses) || empty($addresses) ? ' style="display: none"' : '' ); ?>>
											<?php foreach( $section AS $field ): ?>
												<div>
													<label for="<?php echo $field['input_slug']; ?>"><?php echo lang(substr($field['input_title'], 5)); ?> <?php echo $field['required']; ?>:</label>
													<?php echo $field['input']; ?>
													<?php echo ( form_error($field['input_slug']) ? '<div class="error">'.form_error($field['input_slug']).'</div>' : '' ); ?>
												</div>
											<?php endforeach; ?>
											</div>
										<?php endforeach; ?>
										</div>								
									</td>
								</tr>
							</table>
						</fieldset>
						<?php endif; ?>
					</div><!-- END BILLING DETAILS -->
                
                    
                    <div class="col-sm-4">
                        <h5 <?php echo ( ! $valid_gateway ? ' class="error"' : '' ); ?> class="bottom-buffer"><?php echo lang('firesale:checkout:title:payment_method'); ?></h5>
                        <fieldset<?php echo ( ! $valid_gateway ? ' class="error"' : '' ); ?>>

                            <p><?php echo lang('firesale:checkout:select_payment_method'); ?></p>
                            <?php foreach( $this->gateways->get_enabled() as $gateway_id => $gateway ): ?>
                                <div>
                                    <label for="gateway_<?php echo $gateway_id; ?>">
                                        <input type="radio" name="gateway" id="gateway_<?php echo $gateway_id; ?>" value="<?php echo $gateway_id; ?>" <?php echo set_radio('gateway', $gateway_id); ?> />
                                        <strong><?php echo $gateway['name']; ?></strong>
                                    </label>
                                </div>
                            <?php endforeach; ?>

                            <button type="submit" name="btnAction" value="pay" class="btn bg-green color-white bottom-buffer"><span><?php echo lang('firesale:checkout:submit_and_pay'); ?></span></button>
                        </fieldset>
                    </div>
					
					
                </div>
