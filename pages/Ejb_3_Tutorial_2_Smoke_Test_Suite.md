---
title: Ejb_3_Tutorial_2_Smoke_Test_Suite
---
Here is a quick test suite to verify that things basically work. To create this test:
* Expand your project (**Ejb3Tutorial2**)
* Select the **test** source folder
* Right-click and select **New:Class**
* Enter **dao.impl** for the **Package**
* Enter **CompanyDaoImplTest** for the **Class name**
* Click **Finish**
* Enter the following class
* Once the class is saved and compiles, execute it (Right-click in the file, select **Run As::JUnit Test**

### CompanyDaoImplTest.java
{% highlight java %}
package dao.impl;

import static org.junit.Assert.assertEquals;
import static org.junit.Assert.assertNotNull;

import org.junit.Before;
import org.junit.BeforeClass;
import org.junit.Test;

import util.JBossUtil;
import dao.CompanyDao;
import entity.Address;
import entity.Company;
import entity.Person;

public class CompanyDaoImplTest {
    private CompanyDao dao;

    @BeforeClass
    public static void setupJboss() throws Exception {
        JBossUtil.startDeployer();
    }

    @Before
    public void getCompanyDao() throws Exception {
        dao = JBossUtil.lookup(CompanyDao.class, "CompanyDao/local");
    }

    private Company createCompanyImpl() {
        final Address a = new Address("5080 Spectrum Drive", "Suite 700 West",
                "Addison", "TX", "75001");
        final Company c = new Company("Valtech", a);
        dao.createCompany(c);
        return c;
    }

    @Test
    public void createCompany() {
        final Company c = createCompanyImpl();
        assertEquals("Valtech", c.getName());
        assertNotNull(c.getId());
    }

    @Test
    public void hirePerson() {
        final Address a = new Address("5080 Spectrum Drive", "Suite 700 West",
                "Addison", "TX", "75001");
        final Person p = new Person("Brett", 'L', "Schuchert", a);
        final Company c = createCompanyImpl();
        c.hire(p);
        dao.update(c);
        final Company found = dao.find(c.getId());
        assertEquals(1, found.getEmployees().size());
    }
}
{% endhighlight %}
