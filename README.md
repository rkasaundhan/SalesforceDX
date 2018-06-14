trigger CreateInvoiceDetail on Invoice_US__c (after insert,after update,after delete) 
{
    //Insert Operation
    List<String> Orderslst = new  List<String> ();
    set<String> PONames;
    Map<Id, List<String>> mapofStrings= new Map<Id, List<String>>();
    for(Invoice_US__c Invobj: Trigger.new)
    {
        Orderslst = Invobj.Purchase_Orders_Multiple__c.split(','); 
        mapofStrings.put(Invobj.Id, Orderslst);
        

    }
    
    List<Invoice_Detail__c> InvoiceList = new List<Invoice_Detail__c>();
    for(Purchase_Order_New__c  PurchaseObj : [Select Id,Name from Purchase_Order_New__c where name IN :Orderslst])
    {
        for (Id id1 : mapofStrings.keySet())
        {    
           
            System.debug('Invoice'+id1);
            System.debug('Purchase_Order_New__c'+PurchaseObj.Id);
            Invoice_Detail__c InvoiceRecord = new Invoice_Detail__c();
            InvoiceRecord.Invoice__c = id1;
            InvoiceRecord.PO_ID__c = PurchaseObj.Id;
            InvoiceList.add(InvoiceRecord);
        }
    }
    System.debug('InvoiceList @@@@@@@@@@@@@'+InvoiceList);
    if(InvoiceList != null && !InvoiceList .isEmpty())
      {
         insert InvoiceList;
      }
      
     //update Operation 
     List<String> oldList = new List<string>();
     List<String> newList = new List<string>();
     for (Invoice_US__c  PO : Trigger.new)
     {
        Invoice_US__c Oldid = Trigger.oldMap.get(PO.ID);
        if(PO.Purchase_Orders_Multiple__c!= Oldid.Purchase_Orders_Multiple__c) 
         {
              oldList.add(Oldid.Purchase_Orders_Multiple__c);
              newList.add(PO.Purchase_Orders_Multiple__c);
         }
            
        }
        System.debug('oldList'+oldList);
        System.debug('newList'+newList);
}
