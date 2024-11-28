//Trigger Class 

trigger OpportunityTrigger on Opportunity (After insert , After update , After delete) {
    if(Trigger.isInsert){
        if(Trigger.isAfter){
            OpportunityTriggerHandler.FetchMaxANDMinAmountOfRelatedOpportunity(Trigger.new);
        }
    }
    if(Trigger.isUpdate){
        if(Trigger.isAfter){
            OpportunityTriggerHandler.FetchMaxANDMinAmountOfRelatedOpportunity(Trigger.new);
        }
    }
    if(Trigger.isDelete){
        if(Trigger.isAfter){
            OpportunityTriggerHandler.FetchMaxANDMinAmountOfRelatedOpportunity(Trigger.old);
        }
    }
}

<-------------------------------------------------------------------------------------------------------------------------------------------------------------------------->
//Handler Class 

public class OpportunityTriggerHandler {
  public static void FetchMaxANDMinAmountOfRelatedOpportunity(List<Opportunity> oppList){
        Set<Id> accIds = new Set<Id>();
        for(Opportunity Opp : oppList){
            accIds.add(Opp.AccountId);
        }
        List<Account> accList = new List<Account>();
        List<AggregateResult> aggrList = [SELECT AccountId accIds , Max(Amount) max , Min(Amount) min FROM Opportunity WHERE AccountId IN : accIds GROUP BY AccountId ];
        for(AggregateResult aggr : aggrList){
            Account acc = new Account();
            acc.Id = (Id)aggr.get('accIds');
            acc.Max_Amount__c = (Decimal)aggr.get('max');
            acc.Min_Amount__c = (Decimal)aggr.get('min');
            accList.add(acc);
        }
        if(accList.size()>0){
            update accList;
        }
    }
}
