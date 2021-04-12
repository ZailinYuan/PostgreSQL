# Update rows by 'Join' two table
  
  UPDATE Sales_Import SI
  SET AccountNumber = RAN.AccountNumber
  FROM RetrieveAccountNumber RAN
  WHERE RAN.LeadID = SI.LeadID; 
