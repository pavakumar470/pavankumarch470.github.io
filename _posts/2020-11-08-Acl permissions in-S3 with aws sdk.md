---
published: true
---

With Acces Control List in S3 we will have the control over the buckets and the obects that created inside the buckets<br/>
<br/><br/>
Below are permissions that can be provided for the Bucket and the Objects that are available in the Bucket:<br/><br/>
1.Permission.Read<br/>
2.Permission.Write<br/>
3.Permission.FullControl<br/>
4.Permission.ReadAcp-->allows grantee to read object/bucket ACL<br/>
5.Permission.WriteAcp-->allows grantee to write the object/bucket ACL<br/> 
<br/><br/>
For performing the above operations using the Java aws sdk we need to s3 client in java and for the s3 bucket oprations we should have the below jar files downloaded and we should use the same in the classpath:<br/>
1.aws-java-sdk-core-1.11.665.jar<br/>
2.aws-java-sdk-s3-1.11.665.jar<br/>
3.commons-logging-1.2.jar<br/>
4.httpclient-4.5.9.jar<br/>
5.httpcore-4.4.11.jar<br/>
6.jackson-databind-2.10.1.jar<br/>
7.jackson-core-2.10.1.jar<br/>
8.jackson-annotations-2.10.1.jar<br/>
9.joda-time-2.9.2.jar<br/>

<br/><br/>
Below is the sample code for getting and setting the ACL permissions at the bucket and the object level:<br/>
```java
package s3;

import java.util.*;
import com.amazonaws.regions.Regions;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3ClientBuilder;
import com.amazonaws.services.s3.model.AccessControlList;
import com.amazonaws.services.s3.model.CanonicalGrantee;
import com.amazonaws.services.s3.model.Grant;
import com.amazonaws.services.s3.model.Permission;
import com.amazonaws.auth.AWSCredentials;
import com.amazonaws.auth.AWSStaticCredentialsProvider;
import com.amazonaws.auth.BasicAWSCredentials;

public class AWSS3 {
	
	public static void main(String str[]) {
	    System.out.println("============================================================");
	    System.out.println("---------------------Creating the AWS Client----------------");
		AWSCredentials credentials = new BasicAWSCredentials("AKIAPTH3QTKNIFJDSQ","0VIAqX1X89eFysafdsajqGE5drKJbddjs0cU6bWbAFr");
		AmazonS3 s3client = AmazonS3ClientBuilder.standard().withCredentials(new AWSStaticCredentialsProvider(credentials)).withRegion(Regions.US_EAST_1)
				  .build();
		
		    System.out.println("============================================================");
		    System.out.println("---------------------Get Bucket ACL-------------------------");
		    System.out.println(s3client.getBucketAcl("redshift-staging"));
		    AccessControlList acl_bucket = s3client.getBucketAcl("redshift-staging");
		    List<Grant> grants = acl_bucket.getGrantsAsList();
		    for (Grant grant : grants) {
		        System.out.format("  %s: %s\n", grant.getGrantee().getIdentifier(),
		                grant.getPermission().toString());
		    }
		    
		    System.out.println("============================================================");
		    System.out.println("---------------------Set Bucket ACL-------------------------");
		    //we can provide Permission.FullControl,Permission.Read,Permission.Write,Permission.ReadAcp.Permission.WriteAcp
		    
		   acl_bucket.grantPermission(new CanonicalGrantee(s3client.getS3AccountOwner().getId()), Permission.Read);
		   s3client.setBucketAcl("redshift-staging", acl_bucket);
		    System.out.println("============================================================");
		    System.out.println("---------------------Get Object ACL-------------------------");
		    System.out.println(s3client.getObjectAcl("redshift-staging", "folder/"));
		    AccessControlList acl=s3client.getObjectAcl("redshift-staging", "folder/");
		    List<Grant> grants_obj = acl.getGrantsAsList();
		    for (Grant grant : grants_obj) {
		        System.out.format("  %s: %s\n", grant.getGrantee().getIdentifier(),
		                grant.getPermission().toString());
		    }
		    System.out.println("============================================================");
		    System.out.println("---------------------Set Object ACL-------------------------");
		    acl.grantPermission(new CanonicalGrantee(s3client.getS3AccountOwner().getId()),Permission.Read);
		    s3client.setObjectAcl("redshift-staging","folder/",acl);
		    System.out.println();
		    System.out.println();
		    System.out.println("=======================Permission in End====================");
		    System.out.println(s3client.getObjectAcl("redshift-staging", "folder/"));
		    System.out.println("============================================================");
	}
}
```
