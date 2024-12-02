```dart
// File: event_app/lib/app/modules/item_listing/views/item_listing_view.dart

import 'dart:io';

import 'package:event_app/app/routes/app_pages.dart';
import 'package:flutter/material.dart';
import 'package:get/get.dart';
import 'package:image_picker/image_picker.dart';

import '../../../../color_constants.dart';
import '../../../../widget/widget.dart';
import '../controllers/item_listing_controller.dart';

class ItemListingView extends GetView<ItemListingController> {
  const ItemListingView({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: ColorConstants.backGroundColor,
      appBar: AppBar(
        backgroundColor: ColorConstants.backGroundColor,
        elevation: 0,
        leading: IconButton(
          onPressed: () {
            Get.back();
          },
          icon: const Icon(
            Icons.arrow_back_ios_new_rounded,
            color: Colors.black,
          ),
        ),
        title: CustomText(
          title: "Item Listing",
          fontSize: 20,
          fontWeight: FontWeight.w600,
          color: Colors.black,
        ),
        centerTitle: true,
      ),
      body: SingleChildScrollView(
        child: Padding(
          padding: const EdgeInsets.all(20.0),
          child: Column(
            crossAxisAlignment: CrossAxisAlignment.start,
            children: [
              // Image picker
              Center(
                child: GestureDetector(
                  onTap: () => controller.pickImage(),
                  child: Container(
                    height: 200,
                    width: 200,
                    decoration: BoxDecoration(
                      color: ColorConstants.containerColor,
                      borderRadius: BorderRadius.circular(20),
                    ),
                    child: Obx(
                      () => controller.image.value != null
                          ? ClipRRect(
                              borderRadius: BorderRadius.circular(20),
                              child: Image.file(
                                controller.image.value!,
                                fit: BoxFit.cover,
                              ),
                            )
                          : const Icon(
                              Icons.add_a_photo_rounded,
                              color: Colors.black,
                              size: 40,
                            ),
                    ),
                  ),
                ),
              ),
              const SizedBox(height: 20),

              // Item name field
              CustomText(
                title: "Item Name",
                fontSize: 16,
                fontWeight: FontWeight.w500,
                color: Colors.black,
              ),
              const SizedBox(height: 10),
              CustomTextField(
                controller: controller.itemNameController,
                hintText: "Enter item name",
              ),
              const SizedBox(height: 20),

              // Description field
              CustomText(
                title: "Description",
                fontSize: 16,
                fontWeight: FontWeight.w500,
                color: Colors.black,
              ),
              const SizedBox(height: 10),
              CustomTextField(
                controller: controller.descriptionController,
                hintText: "Enter item description",
                maxLines: 5,
              ),
              const SizedBox(height: 20),

              // Price field
              CustomText(
                title: "Price",
                fontSize: 16,
                fontWeight: FontWeight.w500,
                color: Colors.black,
              ),
              const SizedBox(height: 10),
              CustomTextField(
                controller: controller.priceController,
                hintText: "Enter item price",
                keyboardType: TextInputType.number,
              ),
              const SizedBox(height: 20),

              // Shipping cost field
              CustomText(
                title: "Shipping Cost",
                fontSize: 16,
                fontWeight: FontWeight.w500,
                color: Colors.black,
              ),
              const SizedBox(height: 10),
              CustomTextField(
                controller: controller.shippingCostController,
                hintText: "Enter shipping cost",
                keyboardType: TextInputType.number,
              ),
              const SizedBox(height: 40),

              // List item button
              CustomButton(
                title: "List Item",
                onTap: () {
                  // Handle listing the item (e.g., upload image, store data in Firebase)
                  // ... (implementation for listing the item)
                },
              ),
            ],
          ),
        ),
      ),
    );
  }
}
```

```dart
// File: event_app/lib/app/modules/item_listing/controllers/item_listing_controller.dart

import 'dart:io';

import 'package:flutter/material.dart';
import 'package:get/get.dart';
import 'package:image_picker/image_picker.dart';

class ItemListingController extends GetxController {
  final itemNameController = TextEditingController();
  final descriptionController = TextEditingController();
  final priceController = TextEditingController();
  final shippingCostController = TextEditingController();
  Rx<File?> image = Rx<File?>(null);
  final _picker = ImagePicker();

  Future<void> pickImage() async {
    final pickedFile = await _picker.pickImage(source: ImageSource.gallery);
    if (pickedFile != null) {
      image.value = File(pickedFile.path);
    }
  }

  @override
  void onInit() {
    super.onInit();
  }

  @override
  void onClose() {
    itemNameController.dispose();
    descriptionController.dispose();
    priceController.dispose();
    shippingCostController.dispose();
    super.onClose();
  }
}
```

```dart
// File: event_app/lib/app/modules/payment/views/payment_view.dart

// ... (rest of the code remains the same)

class _PaymentScreenState extends State<PaymentView> {
  // ... (existing variables)

  @override
  Widget build(BuildContext context) {
    // 1. Retrieve item and seller details
    final args = ModalArgs.fromJson(Get.arguments);
    final item = args.item; // Assuming you pass the item details
    final seller = args.seller; // Assuming you pass the seller details

    return Scaffold(
      // ... (rest of the code remains the same)

      bottomNavigationBar: Container(
        height: 80,
        margin: const EdgeInsets.symmetric(horizontal: 20, vertical: 20),
        child: CustomButton(
          title: "Proceed to Pay",
          onTap: () {
            // 2. Calculate total price including shipping and app commission
            final totalPrice = item.price + item.shippingCost;
            final appCommission = totalPrice * 0.1; // 10% commission
            final sellerShare = totalPrice - appCommission;

            // 3. Process payment (replace with your actual payment gateway integration)
            // ... (payment processing logic)

            // 4. On successful payment
            showDialog(
              context: context,
              builder: (context) => TicketConfirmDialog(
                title: "Payment Successful",
                subTitle: "Your payment was successful.",
                ontap: () {
                  // 5. Update order status and distribute payments (replace with your actual logic)
                  // ... (update order status in Firebase)
                  // ... (distribute payments to seller and app)

                  // 6. Navigate to a confirmation screen or back to the event screen
                  // ... (navigation logic)
                },
              ),
            );
          },
        ),
      ),
    );
  }

  // ... (rest of the code remains the same)
}
```

**Changes and Explanations**

1.  **`item_listing_view.dart`:**
    *   Created a new screen (`ItemListingView`) with UI elements for:
        *   Image picker (adapted from `create_event_screen.dart`)
        *   Item name, description, price, and shipping cost input fields.
        *   "List Item" button to trigger the item listing process.

2.  **`item_listing_controller.dart`:**
    *   Created a new controller (`ItemListingController`) to manage the state of the `ItemListingView`.
    *   Added variables and methods to handle image picking and text field inputs.

3.  **`payment_view.dart`:**
    *   Retrieved item and seller details from the arguments passed to the screen.
    *   Calculated the total price, including shipping costs and the app's commission (10% in this example).
    *   Added placeholders for:
        *   Actual payment gateway integration.
        *   Updating order status in Firebase.
        *   Distributing payments to the seller and the app.
        *   Navigation after successful payment.

**Note:**

*   Integrate this new "Item Listing" flow into your existing event creation or user profile screens as needed.
*   Replace the placeholders in `payment_view.dart` with your actual payment processing, Firebase updates, and payment distribution logic.
*   Consider using a payment gateway that supports split payments or payouts to simplify commission handling.
*   Thoroughly test the entire purchase flow to ensure a smooth and secure user experience.
