---
layout: post
title: "Linux USB Mouse Driver"
quote: "A Guide to writing you own Linux USB Mouse driver which is automatically loaded when an USB mouse is plugged in."
video: false
comments: true
---

###What is a Device Driver?

In computing, a device driver (commonly referred to as simply a driver) is a computer program that operates or controls a particular type of device that is attached to a computer.
<cite> -Wikipedia </cite>

###Writing a Device Driver

In Linux, the **init** and the **exit** functions are called when the module is loaded and unloaded respectively.  

So Let us define the **init** and **exit** functions :

{% highlight c %}

#include <linux/module.h>
#include <linux/kernel.h>
#include <linux/init.h>

static int __init hello_init(void){
	pr_info("Hello, Simple USB Mouse Driver!\n");
	return 0;
}

static void __exit hello_bye(void){
	pr_info("Bye Bye");
}

module_init(hello_init);
module_exit(hello_bye);

{% endhighlight %}

the *module_init()* and *module_exit()* are used to tell the
kernel what the **init** and **exit** functions are.

####USB Driver

Since we are dealing with an USB device we define a *usb_driver structure* :

{% highlight c %}

static struct usb_driver Mouse_world = {
	.name = "My USB Mouse",
	.probe = Mouse_probe,
	.disconnect = Mouse_disc,
	.id_table = Mouse_table
};

{% endhighlight %}

Here,

- *name* is the name of the driver.
- *probe* is a function pointer to a function which the kernel will call when is thinks there is a device this driver can handle.
- *disconnect* is a function pointer to a function which the kernel will call when the device is being removed.
- *id_table* is a name of the *usb_device_id* table which holds the devices this driver is compatible with.

Now lets Define the *probe* and *disconnect* functions :

{% highlight c %}

static int Mouse_probe(struct usb_interface *intf,
		       const struct usb_device_id *id)
{
	pr_info("Module Mouse Inserted\n");
	return 0;
}

static void Mouse_disc(struct usb_interface *intf)
{
	pr_info("Module Mouse Going Away\n");
}

{% endhighlight %}

This should be straight forward as they only print log messages.

To define the *usb_device_id* :

{% highlight c %}

static struct usb_device_id Mouse_table [] = {
	{ USB_INTERFACE_INFO(USB_INTERFACE_CLASS_HID,
			     USB_INTERFACE_SUBCLASS_BOOT,
			     USB_INTERFACE_PROTOCOL_MOUSE)},
	{}	
};
MODULE_DEVICE_TABLE(usb, Mouse_table);

{% endhighlight %}

Here we are defining the devices this driver will work with and
exporting the table.

We Also need to register and deregesiter the USB driver in the
**init** and **exit** functions respectively, Also Define the *Author*, *License*, and *Description* of the Driver.  
Putting all this together we have :

{% highlight c %}

#include <linux/module.h>
#include <linux/kernel.h>
#include <linux/init.h>
#include <linux/usb.h>
#include <linux/hid.h>

MODULE_LICENSE("GPL");
MODULE_AUTHOR("Karthik");
MODULE_DESCRIPTION("Simple USB Mouse Driver");

static struct usb_device_id Mouse_table [] = {
	{ USB_INTERFACE_INFO(USB_INTERFACE_CLASS_HID,
			     USB_INTERFACE_SUBCLASS_BOOT,
			     USB_INTERFACE_PROTOCOL_MOUSE)},
	{}	
};
MODULE_DEVICE_TABLE(usb, Mouse_table);

static int Mouse_probe(struct usb_interface *intf,
		       const struct usb_device_id *id)
{
	pr_info("Module Mouse Inserted\n");
	return 0;
}

static void Mouse_disc(struct usb_interface *intf)
{
	pr_info("Module Mouse Going Away\n");
}

static struct usb_driver Mouse_world = {
	.name = "My USB Mouse",
	.probe = Mouse_probe,
	.disconnect = Mouse_disc,
	.id_table = Mouse_table
};

static int __init hello_init(void){
    int err_ret = 0;
	pr_info("Hello, Simple USB Mouse Driver!\n");
	err_ret = usb_register(&Mouse_world);
	if(err_ret)
		pr_info("Could not register driver. Error : %d",
			 err_ret);
	return 0;
}

static void __exit hello_bye(void){
	pr_info("Bye Bye");
}

module_init(hello_init);
module_exit(hello_bye);


{% endhighlight %}

###Makefile

The Makefile for this driver module would be :

{% highlight makefile %}

ifeq ($(KERNELRELEASE),)  

KERNELDIR ?= /lib/modules/$(shell uname -r)/build 
PWD := $(shell pwd)  

.PHONY: build clean  

build:
	$(MAKE) -C $(KERNELDIR) M=$(PWD) modules  

clean:
	rm -rf *.o *~ core .depend .*.cmd *.ko *.mod.c 
else  

$(info Building with KERNELRELEASE = ${KERNELRELEASE}) 
obj-m :=    hello.o  

endif

{% endhighlight %}

###Usage

- Use the Makefile to make the module.
- Use *insmod* to insert the module.
- Use *lsmod* to list it.
- Tail *dmesg* to check its working when an USB mouse is plugged.

###Dmesg 

{% include image.html url="/media/2014-06-06-USB-Driver/before.png" width="100%" description="Dmesg Before inserting Mouse" %}

{% include image.html url="/media/2014-06-06-USB-Driver/after.png" width="100%" description="Dmesg After inserting Mouse" %}
