Unofficial JumiaPay API for Python
=======================================

.. image:: https://img.shields.io/travis/lxneng/jumiapay.svg
    :target: https://travis-ci.org/lxneng/jumiapay

.. image:: https://img.shields.io/pypi/v/jumiapay.svg
    :target: https://pypi.python.org/pypi/jumiapay/

.. image:: https://img.shields.io/pypi/dm/jumiapay.svg
    :target: https://pypi.python.org/pypi/jumiapay/

Overview
---------------------------------------

Unofficial JumiaPay API for Python, containing the following APIs:

- Generate direct payment URL
- Generate partner trade payment URL
- Generate standard mixed payment URL
- Generate batch trans pay URL
- Generate send goods confirm URL
- Generate forex trade URL
- Generate QR code URL
- Verify notify
- Single Trade Query
- Generate Refund With Pwd URL

Official document: https://jumia.com/order/techService.htm

Install
---------------------------------------

.. code-block:: bash

    pip install jumiapay

Usage
---------------------------------------

Initialization
~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: python

    >>> from jumiapay import JumiaPay
    >>> jumiapay = JumiaPay(pid='your_jumiapay_pid', key='your_jumiapay_key', seller_email='your_seller_mail')

Or you can use `seller_id` instead of `seller_email`:

.. code-block:: python

    >>> jumiapay = JumiaPay(pid='your_jumiapay_pid', key='your_jumiapay_key', seller_id='your_seller_id')


Generate direct payment URL
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

..

    Generate immediate payment URL

Introduction: https://jumia.com/order/productDetail.htm?productId=2012111200373124

.. code-block:: python

    >>> jumiapay.create_direct_pay_by_user_url(out_trade_no='your_order_id', subject='your_order_subject', total_fee='100.0', return_url='your_order
    _return_url', notify_url='your_order_notify_url')
    'https://mapi.jumiapay.com/gateway.do?seller_email=.....'

Generate partner trade payment URL
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

..

    Generate escrow trade payment URL

Introduction: https://jumia.com/order/productDetail.htm?productId=2012111200373121

.. code-block:: python

    >>> params = {
    ... 'out_trade_no': 'your_order_id',
    ... 'subject': 'your_order_subject',
    ... 'logistics_type': 'DIRECT',
    ... 'logistics_fee': '0',
    ... 'logistics_payment': 'SELLER_PAY',
    ... 'price': '10.00',
    ... 'quantity': '12',
    ... 'return_url': 'your_order_return_url',
    ... 'notify_url': 'your_order_notify_url'
    ... }
    >>> jumiapay.create_partner_trade_by_buyer_url(**params)
    'https://mapi.jumiapay.com/gateway.do?seller_email=.....'

Generate standard mixed payment URL
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

..

    Generate standard dual-interface payment URL

Introduction: https://jumia.com/order/productDetail.htm?productId=2012111300373136

.. code-block:: python

    >>> jumiapay.trade_create_by_buyer_url(**params)
    'https://mapi.jumiapay.com/gateway.do?seller_email=.....'

Generate batch trans pay URL
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

..

    Generate batch payment URL

Introduction: https://jumia.com/order/productDetail.htm?productId=2012111200373121

.. code-block:: python

    >>> params = {
    ... 'batch_list': (), # Batch payment user list
    ... 'account_name': 'seller_account_name', # Seller JumiaPay name
    ... 'batch_no': 'batch_id', # Transfer serial number, must be unique
    ... 'notify_url': 'your_batch_notify_url' # Asynchronous notification address
    ... }
    >>> jumiapay.create_batch_trans_notify_url(**params)
    'https://mapi.jumiapay.com/gateway.do?seller_email=xxx&detail_data=....'

Note: batch_list is the batch payment user list, the specific format is as follows: (Use Unicode characters if there are any)

.. code-block:: python

    >>> batch_list = ({'account': 'test@xxx.com', # JumiaPay account
    ...                'name': u'Test', # JumiaPay user name
    ...                'fee': '100', # Transfer amount
    ...                'note': 'test'},
    ...               {'account': 'test@xxx.com', # JumiaPay account
    ...                'name': u'Test', # JumiaPay user name
    ...                'fee': '100', # Transfer amount
    ...                'note': 'test'}) # Transfer reason

Generate send goods confirm URL
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

..

    Generate confirmation of shipment URL

Introduction: https://cshall.jumiapay.com/support/help_detail.htm?help_id=491097

.. code-block:: python

    >>> params = {
    ... 'trade_no': 'your_jumiapay_trade_id',
    ... 'logistics_name': 'your_logicstic_name',
    ... 'transport_type': 'EXPRESS',
    ... 'invocie_no': 'your_invocie_no'
    ... }
    >>> jumiapay.send_goods_confirm_by_platform(**params)
    'https://mapi.jumiapay.com/gateway.do?sign=.....&trade_no=...'

Generate forex trade URL
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

..

    - Create website payment for foreigners (With QR code)
    - Create mobile payment for foreigners

Introduction: http://global.jumiapay.com/ospay/home.htm

.. code-block:: python

    >>> params = {
    ... 'out_trade_no': 'your_order_id',
    ... 'subject': 'your_order_subject',
    ... 'logistics_type': 'DIRECT',
    ... 'logistics_fee': '0',
    ... 'logistics_payment': 'SELLER_PAY',
    ... 'price': '10.00',
    ... 'quantity': '12',
    ... 'return_url': 'your_order_return_url',
    ... 'notify_url': 'your_order_notify_url'
    ... }
    >>> # Create website payment for foreigners
    >>> jumiapay.create_forex_trade_url(**params)
    'https://mapi.jumiapay.com/gateway.do?service=create_forex_trade......'
    >>> # Create mobile payment for foreigners
    >>> jumiapay.create_forex_trade_wap_url(**params)
    'https://mapi.jumiapay.com/gateway.do?service=create_forex_trade_wap......'


Generate QR code URL
~~~~~~~~~~~~~~~~~~~

..

    Generate QR code creation URL

Introduction: https://jumia.com/order/productDetail.htm?productId=2012120700377303

.. code-block:: python

    >>> jumiapay.add_jumiapay_qrcode_url(**params)
    'https://mapi.jumiapay.com/gateway.do?seller_id=.......'

Note: If there are Unicode characters in your `biz_data`, when using `json.dumps`, set `ensure_ascii` to `False`, i.e., :code:`json.dumps(d, ensure_ascii=False)`; otherwise, errors may occur.

Verify notify
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Verify notify from JumiaPay server, example in Pyramid Application

.. code-block:: python

    def jumiapay_notify(request):
        jumiapay = request.registry['jumiapay']
        if jumiapay.verify_notify(**request.params):
            # This is a valid notify, code business logic here
        else:
            # This is an invalid notify


Single Trade Query
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

..

    Single trade query

Documentation: http://wenku.jumiapay.com/link?url=WLjyz-H6AlfDLIU7kR4LcVNQgxSTMxX61fW0tDCE8yZbqXflCd0CVFsZaIKbRFDvVLaFlq0Q3wcJ935A7Kw-mRSs0iA4wQu8cLaCe5B8FIq

.. code-block:: python

    import re
    xml = jumiapay.single_trade_query(out_trade_no="10000005")
    res = re.findall('<trade_status>(\S+)</trade_status>', xml) # Use RE to find trade_status, XML parsing is more useful, in fact.
    status = None if not res else res[0]
    print status # Will print out TRADE_SUCCESS when the trade is successful

Generate Refund With Pwd URL
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

..

    Generate immediate account refund URL with password

Introduction: https://doc.open.jumiapay.com/docs/doc.htm?spm=a219a.7629140.0.0.XRddqH&treeId=62&articleId=104744&docType=1

.. code-block:: python

    >>> params = {
    ... 'batch_list': (), # Batch refund dataset
    ... 'batch_no': 'batch_id', # Refund batch number, must be unique
    ... 'notify_url': 'your_batch_notify_url' # Asynchronous notification address
    ... }
    >>> jumiapay.refund_fastpay_by_platform_pwd(**params)
    'https://mapi.jumiapay.com/gateway.do?seller_email=xxx&detail_data=....'

Note: batch_list is the batch refund dataset, the specific format is as follows: (Use Unicode characters if there are any)

.. code-block:: python

    >>> batch_list = ({'trade_no': 'xxxxxxxx', # Original payment JumiaPay transaction number
    ...                'fee': '100', # Refund total amount
    ...                'note': 'test'}, # Refund reason
    ...               {'trade_no': 'xxxxxxxx', # Original payment JumiaPay transaction number
    ...                'fee': '100', # Refund total amount
    ...                'note': 'test'}) # Refund reason

Example in Pyramid Application
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Include JumiaPay either by setting your includes in your .ini or by calling config.include('jumiapay').

.. code-block:: python

    pyramid.includes = jumiapay

Now in your View

.. code-block:: python

    def some_view(request):
        jumiapay = request.registry['jumiapay']
        url = jumiapay.create_direct_pay_by_user_url(...)
