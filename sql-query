SELECT 

`receipt_items`.`receipt_id` AS `Receipt ID`,


CASE
    WHEN `bookings`.`status` = 1 THEN "Confirmed"
    WHEN `bookings`.`status` = 2 THEN "Pending"
    WHEN `bookings`.`status` = 3 THEN "Failed"
    WHEN `bookings`.`status` = 4 THEN "Cancelled"
    WHEN `bookings`.`status` = 5 THEN "Amended"
    WHEN `bookings`.`status` = 6 THEN "Pending Amendment"
    WHEN `bookings`.`status` = 20 THEN "Partially Refunded"
    WHEN `bookings`.`status` = 21 THEN "Fully Refunded"
    WHEN `bookings`.`status` = 25 THEN "Redeemed"
    WHEN `bookings`.`status` = 31 THEN "Reserved"
    WHEN `bookings`.`status` = 32 THEN "Expired"
    WHEN `bookings`.`status` = 33 THEN "Rejected"
    WHEN `bookings`.`status` = 41 THEN "Cancellation Requested"
    WHEN `bookings`.`status` = 42 THEN "Refunded"
    WHEN `bookings`.`status` = 43 THEN "Refund Declined"
    WHEN `bookings`.`status` = 99 THEN "Unmapped"
    ELSE `bookings`.`status`
END AS `Booking Status`,

CASE
    WHEN `orders`.`user_id` IS NOT NULL THEN "Registered User"
    WHEN `orders`.`user_id` IS NULL THEN "Guest"
    ELSE `orders`.`user_id`
END AS `User Type`,

CONVERT_TZ(`bookings`.`voucher_email_sent_at`,'+00:00','+08:00') AS `Voucher Email Sent At`,
CONCAT("https://deals.airasia.com/offers/",`offer_skus__via__offer_sku_id`.`offer_id`) AS `offer_id`,
`destinations`.`name` AS `Destination name`,
`translated_offers`.`title` AS `Offer title`,
`offer_skus__via__offer_sku_id`.`title` AS `Variant title`,
`merchants`.`name` AS `Merchant`,
CONVERT_TZ(`certificates`.`created_at`,'+00:00','+08:00') AS `created_at`,
`certificates`.`admin_notes` AS `notes`,
`certificates`.`redemption_code` AS `Booking Ref`,
`bookings`.`partner_reference` as `Alt Booking Ref`,
CONVERT_TZ(`order_items`.`date`,'+00:00','+08:00') AS `Activity Date`,
SUM(`certificate_summaries`.`passengers_count`) AS `Passengers`, 

  SUBSTRING_INDEX(
    SUBSTRING_INDEX(
      contact_detail,
      '"first_name":"',
      -1
    ),
    '",',
    1
  ) AS 'First Name',

  SUBSTRING_INDEX(
    SUBSTRING_INDEX(
      contact_detail,
      '"last_name":"',
      -1
    ),
    '",',
    1
  ) AS 'Last Name',

-- `order_items`.`contact_detail` AS `Contact details`,
SUBSTRING_INDEX(SUBSTRING_INDEX(contact_detail,'"email":"',-1),'"}',1) As `Guest Email`

FROM `certificates`
JOIN `offer_skus` `offer_skus__via__offer_sku_id` ON `certificates`.`offer_sku_id` = `offer_skus__via__offer_sku_id`.`id`
JOIN `translated_offers` ON `offer_skus__via__offer_sku_id`.`offer_id` = `translated_offers`.`id`
JOIN `destinations` ON `translated_offers`.`destination_id` = `destinations`.`id`
JOIN `order_items` ON `certificates`.`order_item_id` = `order_items`.`id`
JOIN `receipt_items` ON `certificates`.`receipt_item_id` = `receipt_items`.`id`
JOIN `receipts` ON `receipt_items`.`receipt_id` = `receipts`.`id`
JOIN `merchants` ON `translated_offers`.`merchant_id` = `merchants`.`id`
JOIN `certificate_summaries` ON `certificate_summaries`.`certificate_id` = `certificates`.`id`
JOIN `bookings` ON `certificates`.`id` = `bookings`.`certificate_id`
JOIN `orders` ON `orders`.`id` = `order_items`.`order_id`

WHERE [[{{date}}]] [[AND {{Created}}]] [[AND {{receipt}}]] [[AND `certificates`.`redemption_code` = {{booking}}]] [[OR `bookings`.`partner_reference` = {{booking}}]] 
[[AND {{offerid}}]] [[AND `Booking Status` = {{Status}}]]

[[AND SUBSTRING_INDEX(SUBSTRING_INDEX(contact_detail,'"first_name":"',-1),'",',1) LIKE CONCAT('%',{{fn}},'%')]]

[[AND SUBSTRING_INDEX(SUBSTRING_INDEX(contact_detail,'"email":"',-1),'"}',1) LIKE CONCAT('%',{{mailing}},'%')]]
  
group by `certificates`.`id`
order by `certificates`.`created_at` desc
