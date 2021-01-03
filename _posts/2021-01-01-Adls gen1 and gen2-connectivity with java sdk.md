---
published: true
---
Recently i have an usecase where i have to run an application which will connect to ADLS GEn1 and ADLS Gen2 to storage. But before using the application i want to make sure that the machine where iam trying to use the application is able to connect to the gen1 and gen2 storage accounts.<br/>

So for connecting to the Gen1 or Gen2 we have to use the Java SDK or we can use the API calls for the same.<br/>

So for making sure that iam able to connect i have developed the below small code for both Gen1 and Gen2 which is a replica of the code used in my application for storage:<br/>

Below applications are helpful in listing files/directories in the path that we are tring to connect.<br/>

Below is the app for Gen1:
```java
package adls_gen1;
import com.microsoft.azure.datalake.store.ADLStoreClient;
import com.microsoft.azure.datalake.store.DirectoryEntry;
import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;
import java.util.List;

public class ADLS_GEN1_TEST {

	public static void main(String str[]) throws Exception{
		String accountFQDN = str[0]; // full account FQDN, not just the account name
		String clientId = str[1];//client ID
		String authTokenEndpoint = str[2];//auth token endpoint url;
		String clientKey = str[3];//client key
		String path=str[4];//path

		AccessTokenProvider provider = new ClientCredsTokenProvider(authTokenEndpoint, clientId, clientKey);
		ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, provider);
		// list directory content
		List<DirectoryEntry> list = client.enumerateDirectory(path, 2000);
		System.out.println("Directory listing for directory /:");
		for (DirectoryEntry entry : list) {
		    printDirectoryInfo(entry); 
		}
		System.out.println("Directory contents listed.");
	}
	private static void printDirectoryInfo(DirectoryEntry ent) {
        System.out.format("Name: %s%n", ent.name);
        System.out.format("  FullName: %s%n", ent.fullName);
        System.out.format("  Length: %d%n", ent.length);
        System.out.format("  Type: %s%n", ent.type.toString());
        System.out.format("  Group: %s%n", ent.group);
        System.out.format("  User: %s%n", ent.user);
        System.out.format("  Permission: %s%n", ent.permission);
        System.out.format("  mtime: %s%n", ent.lastModifiedTime.toString());
        System.out.format("  atime: %s%n", ent.lastAccessTime.toString());
        System.out.println();
    }

}
```

<br/>
Below is the app for Gen2:
```java
package adls_gen2;
//import com.azure.core.credential.TokenCredential;
import com.azure.core.http.rest.PagedIterable;
//import com.azure.storage.common.StorageSharedKeyCredential;
//import com.azure.storage.file.datalake.DataLakeDirectoryClient;
//import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
//import com.azure.storage.file.datalake.models.PathAccessControl;
//import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathItem;
//import com.azure.storage.file.datalake.models.PathPermissions;
//import com.azure.storage.file.datalake.models.RolePermissions;
import com.azure.identity.ClientSecretCredential;
import com.azure.identity.ClientSecretCredentialBuilder;

public class ADLS_GEN2 {

	public static void main(String str[]) {
		String accountName="";//account name
		String clientId="";//client ID
		String ClientSecret="";//client secret key
		String tenantID="";//tenent ID
		String endpoint = "https://" + accountName + ".dfs.core.windows.net";
        String path="path to be connected"
        
	    ClientSecretCredential clientSecretCredential = new ClientSecretCredentialBuilder()
	    .clientId(clientId)
	    .clientSecret(ClientSecret)
	    .tenantId(tenantID)
	    .build();
	           
	    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();
	    DataLakeServiceClient serviceClient=builder.credential(clientSecretCredential).endpoint(endpoint).buildClient();

	    DataLakeFileSystemClient fileSystemClient=serviceClient.getFileSystemClient(path);
		
		ListPathsOptions options=new ListPathsOptions();
	    options.setPath(path);
	    PagedIterable<PathItem> pagedIterable=fileSystemClient.listPaths(options, null);
	    java.util.Iterator<PathItem> iterator = pagedIterable.iterator();
	    PathItem item = iterator.next();
	    while (item != null)
	    {
	        System.out.println(item.getName());
	        if (!iterator.hasNext())
	        {
	            break;
	        }
	        item = iterator.next();
	    }
	}
}
```



